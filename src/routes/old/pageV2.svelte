<script>    
    // Pretty plot components copied from LayerCake
    import { LayerCake, Svg} from 'layercake'; // Import the LayerCake and Svg components from LayerCake
    import ScatterLC from './components/Scatter.LC.svelte';
    import Line from './components/Line.svelte';
    import Area from './components/Area.svelte';
    import AxisXLC from './components/AxisX.LC.svelte';
    import AxisYLC from './components/AxisY.LC.svelte';
    
    import Hero from './hero.svelte';
    
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
    
    <Hero />
    
    
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
    