# Scrolly lemurs!

## Installations

- Create a [sveltekit project](https://svelte.dev/docs/svelte/getting-started): 

```bash
npx sv create 
# Answer the questions. We answered the following
#  project name : scrolly-lemurs
#  project: minimal skeleton
#  package manager: pnpm
#  typing: typescript 
#  modules: drizzle (SQLite, with default value), prettier (linter), tailwind (maybe we'll use it), vercel adapter (for deployment)
cd scrolly-lemurs
pnpm install
pnpm run dev
```
- Open up the local server on your browser, the default address is `http://localhost:5173/`.
- We will also need `LayerCake` and `@rollup/plugin-dsv`
```bash
pnpm install --save layercake
pnpm install @rollup/plugin-dsv --save-dev
pnpm install d3-scale
```
- For `@rollup/plugin-dsv`, we also need to modify `vite.config.js` in the root directory:
```js
import tailwindcss from '@tailwindcss/vite';
import { sveltekit } from '@sveltejs/kit/vite';
import { defineConfig } from 'vite';
import dsv from '@rollup/plugin-dsv';

export default defineConfig({
	plugins: [sveltekit(), tailwindcss(), dsv()]
});
```
> [!TIP]
> Keep an eye on your local server to make sure nothing gets broken as we keep going.
- Copy the [LayerCake](https://layercake.graphics/) components we will use from the web.
  - For simplicity , we will copy them manually. First, we are gonna create the files we need
```bash 
# Creates components/helpers and data dirs
mkdir -p src/routes/components/helpers src/routes/data
# mv to components dir
cd src/routes/components
# create the file
touch Line.svelte Area.svelte AxisX.svelte AxisY.svelte Scatter.svelte
# grab the Scrolly script from the pudding github repo
wget https://raw.githubusercontent.com/the-pudding/svelte-starter/refs/heads/main/src/components/helpers/Scrolly.svelte -O helpers/Scrolly.svelte
# move up one folder, then creates points.csv
cd .. && touch data/points.csv
```
- At this point, you should have a bunch of new files in your working space. We will copy-paste two components from Layer cake:
  - [Line + area](https://layercake.graphics/example/Line). Go ahead and copy paste components into the corresponding empty files.
  - [Scatter](https://layercake.graphics/example/Scatter) (just grab the content from `Scatter.svg.svelte`).

Alriight that was a bit of work but hopefully it was painless. At some point I would like to streamline that process, but for now it is useful to go step-by-step to understand what we're doing. We now start building the main page.

## First LayerCake chart

Lets take it step by step, shall we. I will explain key components of svelte as we go along. You should now open the `src/routes/+page.svelte]`. You should see the content that you can see on your local server. Get rid of it, and we will start by importing the libs.
```svelte
// Within the script tag, we can write javascript code.
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

  // This example loads csv data as json using @rollup/plugin-dsv
  // But for some reason Tweening does't work with this data.
  import points from './data/points.csv';
  
  const xKey = 'myX';
  const yKey = 'myY';

  // Convert the Y-data to numbers.
  points.forEach(d => {
    d[yKey] = +d[yKey];
  });
<script/>
```
- if something failed to download, this is where you will know. Now, we will create a first plot using `points.csv`. Below the script tag.

```svelte
<h1>Hello Scrollytelling!</h1>

Here's an example of a chart made with <a href="">Sveltekit</a> and <a href="https://layercake.graphics/">LayerCake</a>. This is a simple scatter plot by LayerCake:

<div>
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
```
What's happenning? Why are the points stuck at the top? This is where the magic of `CSS` comes to save us. Below the charting code (or wherever really), we can `style` our chart. Here we add that snippet.


```svelte
<!-- Add the chart-container class -->
<div class="chart-container">

    [SAME CODE BUT NOW IT SHOULD WORK]

<div/>

<!-- Define chart-container in style -->
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
```

It doesn't seem like much, but we already did alot! We can add a second plot, to see how LayerCake allows us to quicky build fancy charts from reusable components

```svelte
<!-- FIRST CHART IS HERE. -->

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
```

Notice that here we reuse the same `AxisX` code than in the firs plot. We also use the `Line` and `Area` components, as we would do in typical grammar of graphics style. The `Svg` and `<LayerCake>` tags here take care of some stuff for us, such as making the width of the chart reactive to user's browser (which should work on mobile out of the box!).

## Scrollytelling time

First, we need to add some code within the <script> tag. I'll just put all the code then explain it.

```svelte
<script>
    [SAME STUFF THAN BEFORE]

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
    </script>

[SAME STUFF THAN BEFORE. BELOW THE LAST PLOT]


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
```

At this point, you should see a third plot with some text, but it is messy at the moment. We will make it scrolly in a second.



## References

- [Svelte Scrollytelling Starter](https://svelte.dev/playground/81194f65fdc74601930df7974fb9ffff?version=5.19.10)
- [Tweened values](https://svelte.dev/tutorial/svelte/tweens)
- [$effect](https://svelte.dev/docs/svelte/$effect)
- [Pudding's Scrolly component](https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte)
- [LayerCake](https://layercake.graphics/)