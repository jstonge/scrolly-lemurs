<script>
  import { LayerCake, Svg } from 'layercake'; // Import the LayerCake and Svg components from LayerCake
  import { scaleLinear } from "d3-scale"; // d3 essentially for different scales
  import { Tween } from 'svelte/motion'; // In-house svelte library for dealing with motions

  // Pretty plot components copied from LayerCake
  import Scatter from './components/Scatter.svelte';
  import Line from './components/Line.svelte';
  import Area from './components/Area.svelte';
  import AxisX from './components/AxisX.svelte';
  import AxisY from './components/AxisY.svelte';

  // Scrolly component copied from 
  // https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte
  import Scrolly from "./components/helpers/Scrolly.svelte";
  
  // This example loads csv data as json using @rollup/plugin-dsv
  // But for some reason Tweening does't work with this data.
  import points from './data/points.csv';
  
  const xKey = 'myX';
  const yKey = 'myY';

  // Convert the Y-data to numbers.
  points.forEach(d => {
    d[yKey] = +d[yKey];
  });

    // Global properties of the plots. 
    let width = 400;
  let height = 400;
  
  // Scales for the plots
  let xScale = scaleLinear().domain([0, 10]).range([0, width]);
  let yScale = scaleLinear().domain([0, 10]).range([height, 0]);
  
  
  // this is a reactive variable, that will change based on scrolling progression (see below)
  // TODO: how do we make the data reactive when loaded from a file?
  let data = [
      { foo: 4, bar: 1 },
      { foo: 6, bar: 7 },
      { foo: 9, bar: 5 },
      { foo: 2, bar: 4 },
      { foo: 8, bar: 2 },
      { foo: 9, bar: 9 },
      { foo: 5, bar: 3 },
      { foo: 3, bar: 8 },
      { foo: 1, bar: 6 },
    ];
    
// Data preparation for scrolly telling
  let value = $state();
  // Tween is a helper function to deal with motion associated with scrolling
  const tweenedX = new Tween(data.map((d) => d.foo));
  // This is just one easy example of doing scrollytelling, using same data but slightly different plots
  const setFoo = function () {
    tweenedX.set(data.map((d) => d.foo));
  };
  const setBar = function () {
    tweenedX.set(data.map((d) => d.bar));
  };

  // Based on value changing, we modify the data. 
  // Here we just flip between setFoo and setBar.
  //  https://svelte.dev/docs/svelte/$effect
  $effect(() => {
     if (value == 0) {
      setFoo();
    } else if (value == 1) {
      setBar();
    } else if (value == 2) {
      setFoo();
    }
  })
</script>

<h1>Hello Scrollytelling!</h1>

Here's an example of a chart made with <a href="">Sveltekit</a> and <a href="https://layercake.graphics/">LayerCake</a>. This is a simple scatter plot by LayerCake:

<div class="chart-container">
  <LayerCake 
    x={xKey}
    y={yKey}
    data={points} 
    padding={{ top: 50, right: 50, bottom: 50, left: 50 }}
  >
    <Svg>
      <AxisX />
      <Scatter fill={'steelblue'} r={5} />
    </Svg>
  </LayerCake>
</div>

<p>Here's a second plot, using a combination of "Marks" (such as Line and Area) to make a more sophisticated plot.</p>

<div class="chart-container">
  <LayerCake
      padding={{ top: 50, right: 50, bottom: 50, left: 50 }}
      x={xKey}
      y={yKey}
      yDomain={[0, null]}
      data={points}
    >
      <Svg>
        <AxisX />
        <AxisY ticks={4} />
        <Line />
        <Area />
      </Svg>
    </LayerCake>
</div>

<p>The following is a <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>. As you scroll down, it'll stick to your window.</p>
<div class="chart-container">  
  <!-- Here this is svelte syntax, instead of LayerCake. The two are equivalent. -->
  <svg {width} {height}>
    <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
    {#each data as d, index}
      <!-- tweenedX is nice because it deals with motion here, as data changes -->
      <circle
        cx={xScale(tweenedX.current[index])} 
        cy={yScale(d.bar)}
        r={15}
        fill="steelblue"
        stroke="#000000"
      />
    {/each}
  </svg>
</div>


<section id="scrolly">
  <!-- {value || "-"} when undefined, "-", else put value -->
	<h2>Scrolly <span>{value || "-"}</span></h2>
	<div class="spacer"></div>
	<Scrolly bind:value>
		{#each [0, 1, 2, 3, 4] as text, i}
      <!-- This is where we change values based on a variable active. See CSS. -->
			{@const active = value === i}
			<div class="step" class:active>
          <p>{text}</p>
			</div>
		{/each}
	</Scrolly>
	<div class="spacer"></div>
</section>


<p>Then we keep going after the sticky sequence.</p>


<style>
    /*
    The wrapper div needs to have an explicit width and height in CSS.
    It can also be a flexbox child or CSS grid element.
    The point being it needs dimensions since the <LayerCake> element will
    expand to fill it.
  */
  .chart-container {
    width: 100%;
    height: 250px;
  }
</style>