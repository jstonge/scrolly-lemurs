<script>
import { LayerCake, Svg} from 'layercake'; // Import the LayerCake and Svg components from LayerCake
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
const setFoo = function() {
    tweenedX.set(data.map((d) => d.foo));
};
const setBar = function() {
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

<p>Here's an example of a chart made with <a href="">Sveltekit</a> and <a href="https://layercake.graphics/">LayerCake</a>. This is a simple scatter plot by LayerCake:</p>

<div class="chart-container">
    <LayerCake x={xKey} y={yKey} data={points} padding={{ top: 50, right: 50, bottom: 50, left: 50 }}>
        <Svg>
            <AxisX />
            <Scatter fill={'steelblue'} r={5} />
        </Svg>
    </LayerCake>
</div>

<p>Here's a second plot, using a combination of "Marks" (such as Line and Area) to make a more sophisticated plot.</p>

<div class="chart-container">
    <LayerCake padding={{ top: 50, right: 50, bottom: 50, left: 50 }} x={xKey} y={yKey} yDomain={[0, null]} data={points}>
        <Svg>
            <AxisX />
            <AxisY ticks={4} />
            <Line />
            <Area />
        </Svg>
    </LayerCake>
</div>

<p>The following is a <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>. As you scroll down, it'll stick to your window.</p>
<div class="chart-container-scrolly">
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
    :global(html, body) {
        margin: 0;
        padding: 2rem;
        /* Adds padding around the content */
        background-color: #f9f9f9;
        /* Light background */
        font-family: Arial, sans-serif;
        text-align: center;
        /* Ensures all content is centered */
    }

    /* Ensures spacing between sections */
    section,
    div,
    p {
        margin-top: 2rem;
        /* Adds space between elements */
        margin-bottom: 2rem;
        /* Adds space between elements */
    }

    h1 {
        font-size: 2.5rem;
        font-weight: bold;
        text-align: center;
        color: #333;
        text-transform: uppercase;
        letter-spacing: 2px;
        padding: 10px 20px;
        background: linear-gradient(to right, #ff7e5f, #feb47b);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        border-bottom: 3px solid #ff7e5f;
        display: inline-block;
        margin: 20px auto;
    }


    /*
    The wrapper div needs to have an explicit width and height in CSS.
    It can also be a flexbox child or CSS grid element.
    The point being it needs dimensions since the <LayerCake> element will
    expand to fill it.
  */
    .chart-container {
        width: 50%;
        height: 300px;
        margin: auto;
        top: 10%;
        background: white;
        box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    }

    .chart-container-scrolly {
        width: 400px;
        /* here it works because width of the plot is 400px */
        background: white;
        height: 400px;
        box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
        position: sticky;
        top: 15%;
        right: 30%;
        /* Adds padding from the right side */
        margin-left: auto;
        /* Pushes the container to the right */
    }


    h2 {
        position: sticky;
        top: 4em;
        margin-left: -55%;
    }

    .spacer {
        height: 75vh;
    }

    .step {
        height: 80vh;
        display: flex;
        /* Makes it a flexbox */
        place-items: center;
        /* Centers vertically */
        justify-content: center;
        /* Centers horizontally */
        margin-left: -35%;
        /* Moves it slightly to the left */
    }

    /* This is affect children p of class .step */
    .step p {
        padding: 1em;
        background: whitesmoke;
        color: #ccc;
        border-radius: 5px;
        padding: 0.5rem 1rem;
        display: flex;
        flex-direction: column;
        justify-content: center;
        transition: background 500ms ease;
        box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
        z-index: 10;
    }

    /* We use CSS to change properties based on 'active' state */
    .step.active p {
        background: white;
        color: black;
    }
</style>