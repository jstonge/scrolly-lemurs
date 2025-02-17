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