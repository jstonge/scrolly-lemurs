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
touch Line.svelte Area.svelte AxisX.LC.svelte AxisY.LC.svelte Scatter.svelte
# grab the Scrolly script from the pudding github repo
wget https://raw.githubusercontent.com/the-pudding/svelte-starter/refs/heads/main/src/components/helpers/Scrolly.svelte -O helpers/Scrolly.svelte
# move up one folder, then creates points.csv
cd .. && touch data/points.csv
```
- At this point, you should have a bunch of new files in your working space. We will copy-paste two components from Layer cake:
  - [Line + area](https://layercake.graphics/example/Line). Go ahead and copy paste components into the corresponding empty files.
  - [Scatter](https://layercake.graphics/example/Scatter) (just grab the content from `Scatter.svg.svelte`).

Here's where you can find the components:

<img width="600" alt="Screenshot 2025-02-11 at 9 48 26 AM" src="https://github.com/user-attachments/assets/64f6cb4e-af9b-4a57-ab2d-0cfa1567b1fd" />

Alriight that was a bit of work but hopefully it was painless. At some point I would like to streamline that process, but for now it is useful to go step-by-step to understand what we're doing. We now start building the main page. If ever you get tired, you can find the completed script at the end. You can always just copy paste that into `src/routes/+page.svelte` to see the final result.

## First LayerCake chart

Lets take it step by step, shall we. I will explain key components of svelte as we go along. You should now open the `src/routes/+page.svelte`. You should see the content that you can see on your local server. Get rid of it, and we will start by importing the libs.
```svelte
// Within the script tag, we can write javascript code.
<script>
  import { LayerCake, Svg } from 'layercake'; // Import the LayerCake and Svg components from LayerCake
  import { scaleLinear } from "d3-scale"; // d3 essentially for different scales
  import { Tween } from 'svelte/motion'; // In-house svelte library for dealing with motions

  // Pretty plot components copied from LayerCake
  import Scatter from './components/Scatter.LC.svelte';
  import Line from './components/Line.svelte';
  import Area from './components/Area.svelte';
  import AxisX from './components/AxisX.LC.svelte';
  import AxisY from './components/AxisY.LC.svelte';

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

<img width="902" alt="Screenshot 2025-02-11 at 9 47 36 AM" src="https://github.com/user-attachments/assets/771274ea-0a84-4e3b-b539-2f65423a0b7b" />


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
```

At this point, you should see a third plot with some text, but it is messy at the moment. We will make it scrolly in a second. Once again, the magic will happen in the `<style>` tag. We also updated the `.chart-container` to make it nicer.

```svelte
< style>

/*
    The wrapper div needs to have an explicit width and height in CSS.
    It can also be a flexbox child or CSS grid element.
    The point being it needs dimensions since the <LayerCake> element will
    expand to fill it.
  */
.chart - container {
    width: 50 %;
    height: 300 px;
    margin: auto;
    top: 10 %;
    background: white;
    box - shadow: 1 px 1 px 10 px rgba(0, 0, 0, 0.2);
}

.chart - container - scrolly {
    width: 400 px;
    /* here it works because width of the plot is 400px */
    background: white;
    height: 400 px;
    box - shadow: 1 px 1 px 10 px rgba(0, 0, 0, 0.2);
    position: sticky;
    top: 15 %;
    right: 30 %;
    /* Adds padding from the right side */
    margin - left: auto;
    /* Pushes the container to the right */
}


h2 {
    position: sticky;
    top: 4 em;
    margin - left: -55 %;
}

.spacer {
    height: 75 vh;
}

.step {
    height: 80 vh;
    display: flex;
    /* Makes it a flexbox */
    place - items: center;
    /* Centers vertically */
    justify - content: center;
    /* Centers horizontally */
    margin - left: -35 %;
    /* Moves it slightly to the left */
}

/* This is affect children p of class .step */
.step p {
    padding: 1 em;
    background: whitesmoke;
    color: #ccc;
    border - radius: 5 px;
    padding: 0.5 rem 1 rem;
    display: flex;
    flex - direction: column;
    justify - content: center;
    transition: background 500 ms ease;
    box - shadow: 1 px 1 px 10 px rgba(0, 0, 0, 0.2);
    z - index: 10;
}

/* We use CSS to change properties based on 'active' state */
.step.active p {
    background: white;
    color: black;
}

< /style>
```
At this point, you should be able to scroll down and see the data changing. One additional modification to get reactivity from data loaded from file. We first create a new `data/data.csv` file with this content:

```csv
foo,bar
4,1
6,7
9,5
2,4
8,2
9,9
5,3
3,8
1,6
```

Then we will import it, similar than before

```diff
<script>
+import data from './data/data.csv';
- // this is a reactive variable, that will change based on scrolling progression (see below)
- // TODO: how do we make the data reactive when loaded from a file?
- let data = [
-      { foo: 4, bar: 1 },
-      { foo: 6, bar: 7 },
-      { foo: 9, bar: 5 },
-      { foo: 2, bar: 4 },
-      { foo: 8, bar: 2 },
-      { foo: 9, bar: 9 },
-      { foo: 5, bar: 3 },
-      { foo: 3, bar: 8 },
-      { foo: 1, bar: 6 },
-    ];
</script>

<style>
+ /* In style we will use CSS to manage the transition...  */
+ circle {
+        transition: r 300ms ease, opacity 500ms ease,
+        cx 500ms cubic-bezier(0.76, 0, 0.24, 1),
+        cy 500ms cubic-bezier(0.76, 0, 0.24, 1); /* https://easings.net/#easeInOutQuart */
+        cursor: pointer;
+    }
</style>

```

## Making it pretty

But all of this is quite ugly. Lets make it prettier.

```svelte
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

     [SAME STUFF THAN BEFORE]
< /style>
```

## V2

Ok, I have to be clear, some things weren't working properly with `Tween`. I have to double check, but I think that `Tween` mutates the original data, meaning that when we make irreversible data wrangling move there is no way back to our original data. So we are going to change strategy for now, following [connorrothschild](https://github.com/connorrothschild/better-data-visualizations-with-svelte) approach to distinguish between the `renderedData` and the `initialData`. We will also update the scrolly chart to make it more compelling using, Connor's example of students' grade with respect to number of hour studied. I will diff the update file, explaining it in the comments as we go along

```diff
<script>
// we will make the width reactive. It is a nice way to make our plot reactive to the user's screen width.
+let width = $state(400),
+    height = 400;

// it is good practice to include padding
+const padding = { top: 50, right: 50, bottom: 50, left: 10 };

// instead of using global width, we use innerWidth and innerHeigth now
// Note that we use $derived() instead of $state() to declare reactivity, which is a Svelte5 rune thing.
// I don't want to explain it now, but the gist is that we 'derive' innerWidth from width's reactivity.
+let innerWidth = $derived(width - padding.left - padding.right);
+let innerHeight = height - padding.top - padding.bottom;

// new data file
+import data from './data/study.csv';
-import data from './data/data.csv';

+const padding = {top: 50, right: 50, bottom: 50, left: 10};

// distinguish between state of the data
+let initialData = data;
+let renderedData = $state(initialData);

+let X_MIDPOINT = $state((xScale.domain()[0] + xScale.domain()[1]) / 2);
+let Y_MIDPOINT = $state((yScale.domain()[0] + yScale.domain()[1]) / 2);

// Same story than innerWidth. Also note that here our scaling will be the innerWidth and innerHeight
-let xScale = scaleLinear().domain([0, 10]).range([0, width]);
-let yScale = scaleLinear().domain([0, 10]).range([height, 0]);
+let xScale = $derived(scaleLinear().domain([0, 100]).range([0, innerWidth]));
+let yScale = scaleLinear().domain([0, 70]).range([innerHeight, 0]);

// We will make our scrolly a bit more fancy
+let X_MIDPOINT = $state((xScale.domain()[0] + xScale.domain()[1]) / 2);
+let Y_MIDPOINT = $state((yScale.domain()[0] + yScale.domain()[1]) / 2);

+$effect(() => {
+    if (value == 0) {
+        renderedData = initialData;
+    } else if (value == 1) {
+        renderedData = initialData.map(d => ({
+        ...d,
+        hours: Y_MIDPOINT,
+        grade: X_MIDPOINT
+        }));
+    } else if (value == 2) {
+        renderedData = initialData.map(d => ({ ...d, hours: Y_MIDPOINT }));
+    } else if (value == 3) {
+        renderedData = initialData;
+    } else if (value == 4) {
+        renderedData = initialData.toSorted((a, b) => a.grade - b.grade);
+    } else if (value == 5) {
+        renderedData = initialData;
+    } 
})
-    // Tween is a helper function to deal with motion associated with scrolling
-    const tweenedX = new Tween(data.map((d) => d.foo));
-    // This is just one easy example of doing scrollytelling, using same data but slightly different plots
-    const setFoo = function() {
-        tweenedX.set(data.map((d) => d.foo));
-    };
-    const setBar = function() {
-        tweenedX.set(data.map((d) => d.bar));
-    };
-    
-    // Based on value changing, we modify the data. 
-    // Here we just flip between setFoo and setBar.
-   //  https://svelte.dev/docs/svelte/$effect
-    $effect(() => {
-        if (value == 0) {
-            setFoo();
-        } else if (value == 1) {
-            setBar();
-        } else if (value == 2) {
-            setFoo();
-        }
-    })

// we will abstract away the narrative
+const myNarrative = ['Original data', 'black hole!', 'MidPoint!', 'Back to Original!', 'Grade ordered by studied hours', 'Original!']
</script>

+<div class="chart-container-scrolly" bind:clientWidth={width}>
+    <!-- Here this is svelte syntax, instead of LayerCake. The two are equivalent. -->
+    <svg {width} {height}>
+        <g class="inner-chart" transform="translate({padding.left+10}, {padding.top})">
+            <g class="axis x" transform="translate(0, {innerHeight})">
+                {#each  [0, 25, 50, 75, 100] as tick, index}
+                    <g class='tick' transform="translate({xScale(tick)}, 0)">
+                        <line x1={0} x2={0} y1={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
+                        <text y={6} dy={9} text-anchor={index === 0 ? "start" : "middle"}dominant-baseline="middle">{tick}%</text>
+                    </g>
+                {/each}
+                <text class="axis-title" 
+                    y={-9} 
+                    x={innerWidth} 
+                    text-anchor="end"
+                >Final Grade &rarr;</text
+                >
+            </g>
+            <g class='axis y'>
+                {#each yScale.ticks(4) as tick, index}
+                  <g class='tick' transform="translate(0, {yScale(tick)})">
+                    <line x1={0} x2={innerWidth} y1={0} y2={0} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
+                    <text y={-3}>{index === yScale.ticks(4).length - 1 ? `${tick} hours studied` : tick}</text>
+                  </g>
+                {/each}
+              </g>
+            <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
+            {#each renderedData as d}
+                <circle
+                    cx={xScale(d.grade)} 
+                    cy={yScale(d.hours)}
+                    r={10}
+                    fill="steelblue"
+                    stroke="#000000"
+                />
+            {/each}
+    </g>
+  </svg>
+</div>
-<p>The following is a <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>. As you scroll down, it'll stick to your window.</p>
-<div class="chart-container-scrolly">
-    <!-- Here this is svelte syntax, instead of LayerCake. The two are equivalent. -->
-    <svg {width} {height}>
-        <!-- We loop through the data, drawing the SVG and using d3 to position pixels -->
-        {#each data as d, index}
-      <!-- tweenedX is nice because it deals with motion here, as data changes -->
-      <circle
-        cx={xScale(tweenedX.current[index])} 
-        cy={yScale(d.bar)}
-        r={15}
-        fill="steelblue"
-        stroke="#000000"
-      />
-    {/each}
-  </svg>
-</div>

+ {#each myNarrative as text, i}
- {#each [0, 1, 2, 3, 4] as text, i}
```

Ok, there were many changes, so i might have messed up here and there (I'll clean later). In the meantime, I updated the completed script to refelect V2 version, so can always just copy-paste that. but you can find the old code in `./old`. There is one additional change. Notice that now we have a more fancy, custom plot made with Vanilla Svelte. We can do like LayerCake, and modular parts of the plot into their own component. We will have `./components/AxisX.svelte`

```svelte
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

And `./components/AxisY`

```svelte
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

It is much easier to maintain and improve readibility to do it like that. Then in the main page, we do

```diff
<script>
+import AxisXLC from './components/AxisX.LC.svelte';
-import AxisX from './components/AxisX.LC.svelte';
+import AxisYLC from './components/AxisY.LC.svelte';
-import AxisY from './components/AxisX.LC.svelte';

// Custom svelte
+import AxisX from './components/AxisX.svelte';
+import AxisY from './components/AxisY.svelte';
<script>

+<AxisY width={innerWidth} {yScale} />
-<g class="axis x" transform="translate(0, {innerHeight})">
-    {#each  [0, 25, 50, 75, 100] as tick, index}
-        <g class='tick' transform="translate({xScale(tick)}, 0)">
-            <line x1={0} x2={0} y1={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
-            <text y={6} dy={9} text-anchor={index === 0 ? "start" : "middle"} dominant-baseline="middle">{tick}%</text>
-        </g>
-    {/each}
-    <text class="axis-title" 
-        y={-9} 
-        x={innerWidth} 
-        text-anchor="end"
-    >Final Grade &rarr;</text
-    >
-</g>
+<AxisX height={innerHeight} width={innerWidth} {xScale} />
-<g class='axis y'>
-   {#each yScale.ticks(4) as tick, index}
-     <g class='tick' transform="translate(0, {yScale(tick)})">
-       <line x1={0} x2={innerWidth} y1={0} y2={0} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
-       <text y={-3}>{index === yScale.ticks(4).length - 1 ? `${tick} hours studied` : tick}</text>
-     </g>
-   {/each}
- </g>
```

It is much cleaner now...

## One last module...

We will modularize further by making our custom svelte chart as a component. I will leave it to you to remove the relevant lines from the `+page.svelte` to make it work, this time around.

<details><summary>.components/Scatter.svelte</summary>

```svelte

<script>
    import AxisX from './AxisX.svelte';
    import AxisY from './AxisY.svelte';

    import { scaleLinear } from "d3-scale";
    
    import data from '../data/study.csv';

    let { value, width, height, padding } = $props();
        
    let innerWidth = $derived(width - padding.left - padding.right);
    let innerHeight = height - padding.top - padding.bottom;
    
    // Make data reactive
    let initialData = data;
    let renderedData = $state(initialData);

    // Scales for the plots
    let xScale = $derived(scaleLinear().domain([0, 100]).range([0, innerWidth]));
    let yScale = scaleLinear().domain([0, 70]).range([innerHeight, 0]);
    
    let X_MIDPOINT = $state((xScale.domain()[0] + xScale.domain()[1]) / 2);
    let Y_MIDPOINT = $state((yScale.domain()[0] + yScale.domain()[1]) / 2);

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
            // tweenedX.target = data.map((d) => d.hours);
            renderedData = initialData.map(d => ({
                ...d,
                hours: Y_MIDPOINT
            }));
        } else if (value == 3) {
            // tweenedX.target = rawData.map((d) => d.grade);
            renderedData = initialData;
        } else if (value == 4) {
            // tweenedX.target = rawData.map((d) => d.grade);
            renderedData = initialData.toSorted((a, b) => a.grade - b.grade);
        } else if (value == 5) {
            // tweenedX.target = rawData.map((d) => d.grade);
            renderedData = initialData;
        }
    });
</script>

<svg {width} {height}>
    <g class="inner-chart" transform="translate({padding.left+10}, {padding.top})">
        <AxisY width={innerWidth} {yScale} />
        <AxisX height={innerHeight} width={innerWidth} {xScale} />
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

Here's the complete script, if necessary.

<details><summary>Completed script!</summary>

```svelte
<script>    
// Pretty plot components copied from LayerCake
import { LayerCake, Svg} from 'layercake'; // Import the LayerCake and Svg components from LayerCake
import ScatterLC from './components/Scatter.LC.svelte';
import Line from './components/Line.svelte';
import Area from './components/Area.svelte';
import AxisXLC from './components/AxisX.LC.svelte';
import AxisYLC from './components/AxisY.LC.svelte';

// Custom svelte plot
import Scatter from './components/Scatter.svelte';

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
let width = $state(400),
    height = 400;

const padding = {top: 50, right: 50, bottom: 50, left: 10};

// Make data reactive
let value = $state();

const myNarrative = ['Original data', 'black hole!', 'MidPoint!', 'Back to Original!', 'Grade ordered by studied hours', 'Original!']
</script>

<h1>Hello Scrollytelling!</h1>

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
```

</details>

## References

- [Svelte Scrollytelling Starter](https://svelte.dev/playground/81194f65fdc74601930df7974fb9ffff?version=5.19.10)
- [Tweened values](https://svelte.dev/tutorial/svelte/tweens)
- [$effect](https://svelte.dev/docs/svelte/$effect)
- [Pudding's Scrolly component](https://github.com/the-pudding/svelte-starter/blob/main/src/components/helpers/Scrolly.svelte)
- [LayerCake](https://layercake.graphics/)
