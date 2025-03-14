<script lang="ts">    
	import { getContext } from "svelte";
    import Hero from '$components/Hero.svelte';
    import MysteryPersonForm from '$components/MysteryPersonForm.svelte';
	import LayerCakeTutorial from '$components/LayerCakeTutorial.svelte';
	import ScrollyTutorial from '$components/ScrollyTutorial.svelte';
	import ScrollyDuckTutorial from '$components/ScrollyDuckTutorial.svelte';

    // loads csv data as json using @rollup/plugin-dsv
    import points from "$data/points.csv";
	import study from "$data/study.csv";

    // Global properties of the plots.
    let width = $state(400),
        height = 400;
    
    const padding = {top: 50, right: 50, bottom: 50, left: 10};
    
    let value = $state(); // Make value reactive
    
    const myNarrative = ['Original data', 'black hole!', 'MidPoint!', 'Back to Original!', 'Grade ordered by studied hours', 'Original!']

    function handleSelectionChange(event: CustomEvent<{ cat: string, gender: string }>) {
        // console.log("Selection changed:", event.detail);
    }

</script>
    
<Hero />
<!--  -->
<MysteryPersonForm onchange={(e) => handleSelectionChange(e)} />
<LayerCakeTutorial {points} {padding} />
<ScrollyDuckTutorial />
<ScrollyTutorial {study} {value} {width} {height} {padding} {myNarrative} />

<style>
        :global(html, body) {
            margin: 0;
            background-color: #f9f9f9; /* Light background */
            text-align: center; /* Ensures all content is centered */
        }     
</style>
    
