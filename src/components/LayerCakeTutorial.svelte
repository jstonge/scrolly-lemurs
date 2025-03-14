<script lang="ts">
    import { LayerCake, Svg} from 'layercake'; // Import the LayerCake and Svg components from LayerCake
    
    import ScatterLC from '$components/layercake/Scatter.svelte';
    import Line from '$components/layercake/Line.svelte';
    import Area from '$components/layercake/Area.svelte';
    import AxisXLC from '$components/layercake/AxisX.svelte';
    import AxisYLC from '$components/layercake/AxisY.svelte';

    let { points, padding } = $props();
    
    const xKey = 'myX';
    const yKey = 'myY';
    
    // Convert the Y-data to numbers.
    points.forEach(d => {
        d[yKey] = +d[yKey];
    });
    


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

<style>

        :global(html, body) {
            margin: 0;
            background-color: #f9f9f9; /* Light background */
            text-align: center; /* Ensures all content is centered */
        }

        /* Ensures spacing between sections */
        section,
        p {
            margin-top: 2rem; /* Adds space between elements */
            margin-bottom: 4rem; /* Adds space between elements */
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
    
</style>