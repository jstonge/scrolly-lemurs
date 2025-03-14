# Scrolly lemurs!

I encourage you to create the sveltekit project from scratch instead of cloning the repo. The goal is to understand scrollytelling step by step. Reach to me if anything is unclear! 

> [!WARNING]  
> STILL WIP. There are parts of the code i don't properly explain yet. But it should be enough to get started and study the code.


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
- We will also need `LayerCake`, `d3-array`, `d3-scale`, and `@rollup/plugin-dsv`
```bas
pnpm install --save layercake
pnpm install @rollup/plugin-dsv --save-dev
pnpm install d3-scale d3-array
```
- For `@rollup/plugin-dsv`, we also need to modify `vite.config.ts` in the root directory. We take the opportunity to show how to create a few shortcuts that we will be end up using:

```js
import tailwindcss from '@tailwindcss/vite';
import { sveltekit } from '@sveltejs/kit/vite';
import { defineConfig } from 'vite';
import path from "path";
import dsv from '@rollup/plugin-dsv';

export default defineConfig({
	plugins: [sveltekit(), tailwindcss(), dsv()],
	resolve: {
		alias: {
			$actions: path.resolve("./src/actions"),
			$components: path.resolve("./src/components"),
			$data: path.resolve("./src/data"),
			$routes: path.resolve("./src/routes"),
			$runes: path.resolve("./src/runes"),
			$styles: path.resolve("./src/styles"),
			$svg: path.resolve("./src/svg"),
			$utils: path.resolve("./src/utils")
		}
	}
});
```

> [!TIP]
> Keep an eye on your local server to make sure nothing gets broken as we keep going.
- Copy the [LayerCake](https://layercake.graphics/) components we will use from the web.
  - For simplicity , we will copy them manually. First, we are gonna create the files we need
```bash 
# Creates components/helpers and data dirs
mkdir -p src/routes/components/helpers src/routes/data src/routes/components/layercake
# mv to components dir
cd src/routes/components/layercake
# create the file
touch Line.svelte Area.svelte AxisX.svelte AxisY.svelte Scatter.svelte
# grab the Scrolly script from the pudding github repo
wget https://raw.githubusercontent.com/the-pudding/svelte-starter/refs/heads/main/src/components/helpers/Scrolly.svelte -O helpers/Scrolly.svelte
# move up one folder, then creates points.csv
cd ../.. && touch data/points.csv
```
- At this point, you should have a bunch of new files in your working space. We will copy-paste two components from Layer cake:
  - [Line + area](https://layercake.graphics/example/Line). Go ahead and copy paste components into the corresponding empty files.
  - [Scatter](https://layercake.graphics/example/Scatter) (just grab the content from `Scatter.svg.svelte`).

Here's where you can find the components:

<img width="600" alt="Screenshot 2025-02-11 at 9 48 26 AM" src="https://github.com/user-attachments/assets/64f6cb4e-af9b-4a57-ab2d-0cfa1567b1fd" />

Alriight that was a bit of work but hopefully it was painless. At some point I would like to streamline that process, but for now it is useful to go step-by-step to understand what we're doing. We now start building the main page. If ever you get tired, you can find the completed script at the end. You can always just copy paste that into `src/routes/+page.svelte` to see the final result.

## First LayerCake chart

> [!TIP]
> Actually, if you feel like it, you can go glimpse at Sveltekit's [Introduction](https://svelte.dev/tutorial/svelte/your-first-component) tutorial. No need to understanding everything, just to have a vibe for what it is doing.

Lets take it step by step, shall we. I will explain key components of svelte as we go along. You should now open the `src/routes/+page.svelte`. You should see the content that you can see on your local server. Get rid of it, and we will start by importing the libs.
```svelte
// Within the script tag, we can write javascript code.
<script>
  import { LayerCake, Svg } from 'layercake'; // Import the LayerCake and Svg components from LayerCake
  import { scaleLinear } from "d3-scale"; // d3 essentially for different scales

  // Pretty plot components copied from LayerCake
  // the dollar sign here is an alias defined in the config file earlier 
  import Scatter from  '$components/layercake/Scatter.svelte';
  import Line  from    '$components/layercake/Line.svelte';
  import Area  from    '$components/layercake/Area.svelte';
  import AxisX from    '$components/layercake/AxisX.svelte';
  import AxisY from    '$components/layercake/AxisY.svelte';

  // Scrolly component copied from 
  // https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte
	import Scrolly from "$components/helpers/Scrolly.svelte";
  
  // This example loads csv data as json using @rollup/plugin-dsv
  import points from '$data/points.csv';
  
  const xKey = 'myX';
  const yKey = 'myY';

  // Convert the Y-data to numbers.
  points.forEach(d => {
    d[yKey] = +d[yKey];
  });
</script>
```
- if something failed to download, this is where you will know. Now, we will create a first plot using `points.csv`. Below the script tag.

```svelte
<h1>Hello Scrollytelling!</h1>

<p>Here's an example of a chart made with <a href="">Sveltekit</a> and <a href="https://layercake.graphics/">LayerCake</a>. This is a simple scatter plot by LayerCake:</p>

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


```diff
+<div class="chart-container">
-<div>

+<!-- Define chart-container in style -->
+<style>
+    /*
+    The wrapper div needs to have an explicit width and height in CSS.
+    It can also be a flexbox child or CSS grid element.
+    The point being it needs dimensions since the <LayerCake> element will expand to fill it.
+  */
+  .chart-container {
+    width: 100%;
+    height: 250px;
+  }
+</style>
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

<img width="902" alt="Screenshot 2025-02-11 at 9 47 36 AM" src="https://github.com/user-attachments/assets/771274ea-0a84-4e3b-b539-2f65423a0b7b" />

## Making it pretty

But all of this is quite ugly. Lets make it prettier (with a little help from our friend ChatGPT or Claude. In general, LLMs are pretty good at CSS and HTML, as these are well established technology. That say, CSS in recent years became much cooler, so it is worth to understand it at some point to use most recent features, which tend to be less clunky).

```diff
<style>
+    :global(html, body) {
+        margin: 0;
+        padding: 2rem; /* Adds padding around the content */
+        background-color: #f9f9f9; /* Light background */
+        font-family: Arial, sans-serif;
+        text-align: center; /* Ensures all content is centered */
+    }
+
+    /* Ensures spacing between sections */
+    section,
+    div,
+    p {
+        margin-top: 2rem; /* Adds space between elements */
+        margin-bottom: 2rem; /* Adds space between elements */
+    }
+
+    h1 {
+        font-size: 2.5rem;
+        font-weight: bold;
+        text-align: center;
+        color: #333;
+        text-transform: uppercase;
+        letter-spacing: 2px;
+        padding: 10px 20px;
+        background: linear-gradient(to right, #ff7e5f, #feb47b);
+        -webkit-background-clip: text;
+        -webkit-text-fill-color: transparent;
+        border-bottom: 3px solid #ff7e5f;
+        display: inline-block;
+        margin: 20px auto;
+    }
</style>
```

## Scrollytelling time

It is time to make our chart reacting to scrolling. We will make a simple scatter plot, with the data being manipulated in different ways depending on where users are in the scrolling adventure. This is where the Svelte stuff really kicks in. I'll put the code and explain after

> [!TIP]
> Actually, it is be a good place to take a break and go glimpse at Sveltekit's [Reactivity](https://svelte.dev/tutorial/svelte/state) tutorial. No need to understanding everything, just to have a vibe for what it is doing.

```svelte
<script>
[ STUFF LIKE BEFORE ]

// $state() is a rune. This will make our plot width reactive. 
// That is, it'll make the plot width adjusted to the user's screen width.
// Reactivity is what makes Svelte and other front end frameworks unique.
// Here, the default value is 400px.
let width = $state(400);
let height = 400;


// Instead of directly using width, we declare innerWidth and innerHeigth
// based on some padding. Note that we use $derived() instead of $state() here because innerWidth is `derived` from width.
const padding = { top: 50, right: 50, bottom: 50, left: 10 };
let innerWidth = $derived(width - padding.left - padding.right);
let innerHeight = height - padding.top - padding.bottom;

// new data file
import data from '$data/study.csv';

// Data preparation for scrolly telling ---

// this value variable is key. It'll contain the scroll position by way
// of [CSS stick](https://developer.mozilla.org/en-US/docs/Web/CSS/position) property.
let value = $state();

// distinguish between state of the data
let initialData = data;
let renderedData = $state(initialData);

 // Same story than innerWidth. Also note that here our scaling will be the innerWidth and innerHeight
let xScale = $derived(scaleLinear().domain([0, 100]).range([0, innerWidth]));
let yScale = scaleLinear().domain([0, 70]).range([innerHeight, 0]);

// A Change in value that we will use in our scrollytelling
let X_MIDPOINT = $state((xScale.domain()[0] + xScale.domain()[1]) / 2);
let Y_MIDPOINT = $state((yScale.domain()[0] + yScale.domain()[1]) / 2);

// Last rune that you'll ever see, which is said to be used with care
// We use $effect() to change state of our data, based on where we are in the stepping. Still tbd if this is the best way to do it but it works.
$effect(() => {
    if (value == 0) {
        renderedData = initialData;
    } else if (value == 1) {
        // When user gets to value==1, we modify the data as follow (JS is annoying).
        renderedData = initialData.map(d => ({
          ...d,
          hours: Y_MIDPOINT,
          grade: X_MIDPOINT
        }));
    } else if (value == 2) {
        // Change one of the two value.
        renderedData = initialData.map(d => ({ ...d, hours: Y_MIDPOINT }));
    } else if (value == 3) {
      // initialData has not been mutated, which is a good thing.
      renderedData = initialData;
    } else if (value == 4) {
        renderedData = initialData.toSorted((a, b) => a.grade - b.grade);
    } else if (value == 5) {
        renderedData = initialData;
    } 
})

// We will abstract away the narrative
const myNarrative = [
  'Original data', 
  'black hole!', 
  'MidPoint!', 
  'Back to Original!', 
  'Grade ordered by studied hours', 'Original!'
]
</script>

[SAME LAYERCAKE CHART AS BEFORE]

<p>The following is a <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>. As you scroll down, it'll stick to your window.</p>

<div class="chart-container-scrolly" bind:clientWidth={width}>
    <!-- Here this is svelte syntax, instead of LayerCake. The two are equivalent. -->
    <svg {width} {height}>
        <g class="inner-chart" transform="translate({padding.left10}, {padding.top})">
            <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
            {#each renderedData as d}
                <circle
                    cx={xScale(d.grade)} 
                    cy={yScale(d.hours)}
                    r={10}
                    fill="steelblue"
                    stroke="#000000"
                />
            {/each}
    </g>
  </svg>
</div>

<section id="scrolly">
  <!-- {value || "-"} when undefined, "-", else put value -->
	<h2>Scrolly <span>{value || "-"}</span></h2>
	<div class="spacer"></div>
	<Scrolly bind:value>
		{#each myNarrative as text, i}
      <!-- This is where we change values based on a variable active. See CSS. -->
			{@const active = value === i}
			<div class="step" class:active>
          <p>{text}</p>
			</div>
		{/each}
	</Scrolly>
	<div class="spacer"></div>
</section>
```

At this point, you should see a third plot with some text, but it is messy at the moment. We will make it scrolly in a second. Once again, the magic will happen in the `<style>` tag. We also updated the `.chart-container` to make it nicer (you can copy paste the whole thing if you want)

```svelte
<style>

  :global(html, body) {
      margin: 0;
      padding: 2rem;/* Adds padding around the content */
      background-color: #f9f9f9; /* Light background */
      font-family: Arial, sans-serif;
      text-align: center; /* Ensures all content is centered */
  }

  /* Ensures spacing between sections */

  section,
  div,
  p {
      margin-top: 2rem; /* Adds space between elements */
      margin-bottom: 2rem; /* Adds space between elements */
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

  /* SCROLLY STUFF */

  .chart-container-scrolly {
      width: 40%;
      background: white;
      height: 400px;
      box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
      position: sticky;
      top: 15%;
      right: 15%;
      margin-left: auto;
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

< /style>
```

At this point, you should be able to scroll down and see the data changing! But the transitions are not smooth yet. There is a couple of ways to handle that. One that I know is the following:

```diff
+ /* In style we will use CSS to manage the transition...  */
+ circle {
+        transition: r 300ms ease, opacity 500ms ease,
+        cx 500ms cubic-bezier(0.76, 0, 0.24, 1),
+        cy 500ms cubic-bezier(0.76, 0, 0.24, 1); /* https://easings.net/#easeInOutQuart */
+        cursor: pointer;
+    }
```

taddah. Another way to do it to use [Tweening](https://svelte.dev/tutorial/svelte/tweens) in Svelte.  

## Modularizing further

By now, you should realize how scrollytelling can get messy fast. We can do the same trick that `LayerChart` is doing, with composing modules that we can call. To do that, we will create a new component in `./components/Scatter.svelte`

```svelte
 <script lang="ts">
    
    import { scaleLinear } from "d3-scale";
    
    import data from '$data/study.csv';

    // Main change; you can think of $props() as the arguments
    // this file needs to run. Here we need value (the stepping),
    // width, height, and padding. These are states share across files.
    let { value, width, height, padding } = $props();
        
    let innerWidth = $derived(width - padding.left - padding.right);
    let innerHeight = height - padding.top - padding.bottom;
    
    // Make data reactive. If the study data was shared with other components, 
    // we could make it a prop as well. Maybe we'll do that later.
    let initialData = data;
    let renderedData = $state(initialData);

    // Scales for the plots
    let xScale = $derived(scaleLinear().domain([0, 100]).range([0, innerWidth]));
    let yScale = scaleLinear().domain([0, 70]).range([innerHeight, 0]);
    
    let X_MIDPOINT = (xScale.domain()[0] + xScale.domain()[1]) / 2;
    let Y_MIDPOINT = (yScale.domain()[0] + yScale.domain()[1]) / 2;

    $effect(() => {
        if (value == 0) {
        renderedData = initialData;
        } else if (value == 1) {
            renderedData = initialData.map(d => ({
                ...d,
                hours: Y_MIDPOINT,
                grade: X_MIDPOINT
            }));
        } else if (value == 2) {
            renderedData = initialData.map(d => ({
                ...d,
                hours: Y_MIDPOINT
            }));
        } else if (value == 3) {
            renderedData = initialData;
        } else if (value == 4) {
            renderedData = initialData.toSorted((a, b) => a.grade - b.grade);
        } else if (value == 5) {
            renderedData = initialData;
        }
    });
</script>

<svg {width} {height}>
    <g class="inner-chart" transform="translate({padding.left+10}, {padding.top})">
        <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
        {#each renderedData as d}
            <circle
                cx={xScale(d.grade)} 
                cy={yScale(d.hours)}
                r={10}
                fill="steelblue"
                stroke="#000000"
            />
        {/each}
</g>
</svg>

<style>
    circle {
        transition: r 300ms ease, opacity 500ms ease,
        cx 500ms cubic-bezier(0.76, 0, 0.24, 1),
        cy 500ms cubic-bezier(0.76, 0, 0.24, 1); /* https://easings.net/#easeInOutQuart */
        cursor: pointer;
    }
</style>
```

Now, we can call that component as such:

```diff
<script>
- import Scatter from '$components/layerchart/Scatter.LC.svelte';
+ import ScatterLC from '$components/layerchart/Scatter.LC.svelte';

+ import Scatter from '$components/Scatter.svelte';

[SAME STUFF THAN BEFORE]

</script>

# we modify our layerChart scatter...
-<Scatter fill={'steelblue'} r={5} />
+<ScatterLC fill={'steelblue'} r={5} />

<div class="chart-container-scrolly" bind:clientWidth={width}>
-    <!-- Here this is svelte syntax, instead of LayerCake. The two are equivalent. -->
-    <svg {width} {height}>
-        <g class="inner-chart" transform="translate({padding.left10}, {padding.top})">
-            <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
-            {#each renderedData as d}
-                <circle
-                    cx={xScale(d.grade)} 
-                    cy={yScale(d.hours)}
-                    r={10}
-                    fill="steelblue"
-                    stroke="#000000"
-                />
-            {/each}
-    </g>
-  </svg>
+    <Scatter {value} {width} {height} {padding} />
</div>
```

Since the name of the variables are the same than the props, we don't need to name the arguments (aka `<Scatter value={value} widht={width} height={height} padding={padding} />`). But you could. We don't do that just because we're fancy. It is to keep our main `+page.svelte` as readble as possible. I usually start with prototyping on the main page, before modularizing. Right now our Scatter plot is not really pretty. We can create axis in the Scatter plot as follows

```diff
// Scatter.svelte
<script>
+let xTicks = [0, 25, 50, 75, 100];
+let yTicks = yScale.ticks(4);
</script>

<svg {width} {height}>
    <g class="inner-chart" transform="translate({padding.left+10}, {padding.top})">
+    // Pretty X-AXIS
+    <g class="axis x" transform="translate(0, {innerHeight})">
+        {#each xTicks as tick, index}
+          <g class='tick' transform="translate({xScale(tick)}, 0)">
+            <line x1={0} x2={0} y1={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
+            <text y={6} dy={9} text-anchor={index === 0 ? "start" : "middle"} dominant-baseline="middle">{tick}%</text>
+          </g>
+        {/each}
+        <text class="axis-title" 
+              y={-9} 
+              x={innerWidth} 
+              text-anchor="end"
+          >Final Grade &rarr;</text
+        >
+      </g>
+      // Pretty Y-AXIS
+       <g class='axis y'>
+           {#each yTicks as tick, index}
+             <g class='tick' transform="translate(0, {yScale(tick)})">
+               <line x1={0} x2={innerWidth} y1={0} y2={0} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
+               <text y={-3}>{index === yTicks.length - 1 ? `${tick} hours studied` : tick}</text>
+             </g>
+           {/each}
+         </g>
        <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
        {#each renderedData as d}
            <circle
                cx={xScale(d.grade)} 
                cy={yScale(d.hours)}
                r={10}
                fill="steelblue"
                stroke="#000000"
            />
        {/each}
</g>
</svg>
```

Here we are borrowing the code from [connorrothschild](https://www.youtube.com/watch?v=-THp2YVYEFc&pp=ygUXY29ubm9yIHJvdHNjaGlsZCBzdmVsdGU%3D) and his [github](https://github.com/connorrothschild/better-data-visualizations-with-svelte/tree/72c9194899fe1e8ecd7faa52e1b24d4b7a61ef53). He does an amazing job explaning it. Go see his talk for an explanation. We'll just add the code here and keep going. But we can modularize further, creating components for our axes! We create `./components/axisY.svelte` 

```
// axisY.svelte
<script>
    let {width, yScale } = $props();
    
    let yTicks = yScale.ticks(4);
  </script>
  
  <g class='axis y'>
    {#each yTicks as tick, index}
      <g class='tick' transform="translate(0, {yScale(tick)})">
        <line x1={0} x2={width} y1={0} y2={0} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
        <text y={-3}>{index === yTicks.length - 1 ? `${tick} hours studied` : tick}</text>
      </g>
    {/each}
  </g>
```

and `./components/axisX.svelte`

```svelte
// axisX.svelte
<script>
    let { height, width, xScale } = $props();
    
    let xTicks = [0, 25, 50, 75, 100];
  </script>
  
  <g class="axis x" transform="translate(0, {height})">
    {#each xTicks as tick, index}
      <g class='tick' transform="translate({xScale(tick)}, 0)">
        <line x1={0} x2={0} y1={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
        <text y={6} dy={9} text-anchor={index === 0 ? "start" : "middle"} dominant-baseline="middle">{tick}%</text>
      </g>
    {/each}
    <text class="axis-title" 
          y={-9} 
          x={width} 
          text-anchor="end"
      >Final Grade &rarr;</text
    >
  </g>
```

which we call

```diff
<script>
+import AxisX from './AxisX.svelte';
+import AxisY from './AxisY.svelte';
</script>

<svg {width} {height}>
    <g class="inner-chart" transform="translate({padding.left+10}, {padding.top})">
-    // Pretty X-AXIS
+    <AxisX height={innerHeight} width={innerWidth} {xScale} />
-    <g class="axis x" transform="translate(0, {innerHeight})">
-        {#each xTicks as tick, index}
-          <g class='tick' transform="translate({xScale(tick)}, 0)">
-            <line x1={0} x2={0} y1={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
-            <text y={6} dy={9} text-anchor={index === 0 ? "start" : "middle"} dominant-baseline="middle">{tick}%</text>
-          </g>
-        {/each}
-        <text class="axis-title" 
-              y={-9} 
-              x={innerWidth} 
-              text-anchor="end"
-          >Final Grade &rarr;</text
-        >
-      </g>
-      // Pretty Y-AXIS
+      <AxisY width={innerWidth} {yScale} />
-       <g class='axis y'>
-           {#each yTicks as tick, index}
-             <g class='tick' transform="translate(0, {yScale(tick)})">
-               <line x1={0} x2={innerWidth} y1={0} y2={0} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
-               <text y={-3}>{index === yTicks.length - 1 ? `${tick} hours studied` : tick}</text>
-             </g>
-           {/each}
-         </g>
        <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
        {#each renderedData as d}
            <circle
                cx={xScale(d.grade)} 
                cy={yScale(d.hours)}
                r={10}
                fill="steelblue"
                stroke="#000000"
            />
        {/each}
</g>
</svg>
```

Scrollytelling requires different technologies, which make it somewhat challenging to learn. But at the same time, to get started is straightforward enough:

- `<script>`: Javascript code to load the data, initialize, and declare state variables that will change during the experience. Svelte5's runes are necessary to make the reactivity magic happens. We will make that cleaner in a second.
- `body`: We use Svelte (or LayerChart) to plot directly the SVG with `d3-scale` components to help us map the plot to relevant positions. This is more verbose than what you might be used to, but this is a very flexible approach. 
- `<style>`: CSS code to style and user CSS properties to do some magic, such as using the stickiness and doing the 'Hero' page. 

There are details we didn't talk about here. It is long enough already as a readME. I'll add more tutorials on specific aspects of the scrollytelling in `tutorials/`, which assume that you at least got the web app running up to here.

</details>

## Completed script

Here's the completed `+page.svelte` script, if necessary. 

<details><summary>Completed script!</summary>

<script>    
    // Pretty plot components copied from LayerCake
    import { LayerCake, Svg} from 'layercake'; // Import the LayerCake and Svg components from LayerCake
    import ScatterLC from '$components/Scatter.LC.svelte';
    import Line from '$components/Line.svelte';
    import Area from '$components/Area.svelte';
    import AxisXLC from '$components/AxisX.LC.svelte';
    import AxisYLC from '$components/AxisY.LC.svelte';
    
    // Custom svelte plot
    import Scatter from '$components/Scatter.svelte';
    
    // Scrolly component copied from 
    // https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte
    import Scrolly from "$components/helpers/Scrolly.svelte";
    
    // This example loads csv data as json using @rollup/plugin-dsv
    // But for some reason Tweening does't work with this data.
    import points from '$data/points.csv';
    
    const xKey = 'myX';
    const yKey = 'myY';
    
    // Convert the Y-data to numbers.
    points.forEach(d => {
        d[yKey] = +d[yKey];
    });
    
    // Global properties of the plots.
    let width = $state(400),
        height = 400;
    
    const padding = {top: 50, right: 50, bottom: 50, left: 10};
    
    // Make data reactive
    let value = $state();
    
    const myNarrative = ['Original data', 'black hole!', 'MidPoint!', 'Back to Original!', 'Grade ordered by studied hours', 'Original!']
    
    </script>
    
    
    <h1>LayerCake!</h1>
    
    <p>Here's an example of a chart made with <a href="https://svelte.dev/">Sveltekit</a> and <a href="https://layercake.graphics/">LayerCake</a>. This is a simple scatter plot by LayerCake:</p>
    
    <div class="chart-container">
        <LayerCake x={xKey} y={yKey} data={points} {padding}>
            <Svg>
                <AxisXLC />
                <ScatterLC fill={'steelblue'} r={5} />
            </Svg>
        </LayerCake>
    </div>
    
    <p>Here's a second plot, using a combination of "Marks" (such as Line and Area) to make a more sophisticated plot.</p>
    
    <div class="chart-container">
        <LayerCake {padding} x={xKey} y={yKey} yDomain={[0, null]} data={points}>
            <Svg>
                <AxisXLC />
                <AxisYLC ticks={4} />
                <Line />
                <Area />
            </Svg>
        </LayerCake>
    </div>
    
    <p>The following is a <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>. As you scroll down, it'll stick to your window.</p>
    <div class="chart-container-scrolly" bind:clientWidth={width}>
        <!-- Custom svelte component. -->
        <Scatter {value} {width} {height} {padding} />
    </div>
    
    <section id="scrolly">
      <!-- {value || "-"} when undefined, "-", else put value -->
        <h2>Scrolly <span>{value || "-"}</span></h2>
        <div class="spacer"></div>
        <Scrolly bind:value>
            {#each myNarrative as text, i}
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
            background-color: #f9f9f9; /* Light background */
            text-align: center; /* Ensures all content is centered */
        }
    
        /* Ensures spacing between sections */
        section,
        div,
        p {
            margin-top: 2rem; /* Adds space between elements */
            margin-bottom: 2rem; /* Adds space between elements */
        }
    
        h1 {
            font-size: 2.5rem;
            font-weight: bold;
            text-align: center;
            color: #333;
            text-transform: uppercase;
            letter-spacing: 2px;
            padding: 10px 20px;
            background: black;
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
            width: 40%;
            background: white;
            height: 400px;
            box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
            position: sticky;
            top: 15%;
            right: 15%;
            margin-left: auto;
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
    
</details>

## Where next?

- More complex projects require more structure. Here is a few recent examples in the Pudding that I find useful to learn from:
  - [birthday-effect](https://github.com/the-pudding/birthday-effect/tree/main): is a very barebone example that we can use to learn about `russellsamora''s overarching organization (the pudding webste maintainer)
  - [wine-animals](https://github.com/the-pudding/wine-animals): Not Svelte5, but it has all the bell and whistles we might want to learn from. 
  - [middleschool](https://github.com/the-pudding/middleschool/tree/main/src/data): By Alvins Chang, it is a fairly recent, original scrollytelling example using Svelte5. It is worth getting inspired from it, but it is a more elaborate project.
  - [location-game](https://github.com/the-pudding/location-game/tree/main): slightly older project, but it has data wrangling the way they use to do it. I think we can improve on it.

- In-house tutorial
  - [Hero](./tutorials/hero.md)
  - [Mystery Person](./tutorials/mystery-person.md)
  - [Repo organization](./tutorials/repo-organization.md)
  - [Data wranling](./tutorials/duckdb.md)

## References

- [Svelte Scrollytelling Starter](https://svelte.dev/playground/81194f65fdc74601930df7974fb9ffff?version=5.19.10)
- [Tweened values](https://svelte.dev/tutorial/svelte/tweens)
- [$effect](https://svelte.dev/docs/svelte/$effect)
- [Pudding's Scrolly component](https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte)
- [LayerCake](https://layercake.graphics/)
