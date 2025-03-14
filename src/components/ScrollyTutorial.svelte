<script lang="ts">
	import { onMount } from 'svelte';
	import Scatter from '$components/Scatter.svelte';
	import Scrolly from "$components/helpers/Scrolly.svelte";

	// Your existing props
	export let study;
	export let value;
	export let width;
	export let height;
	export let padding;
	export let myNarrative;

	// If you want to delay DB loading until scrolled past
	// set a flag so we only load once
	let dbLoaded = false;

	// A placeholder to store a reference to the sentinel <div>
	let sentinel: HTMLDivElement;

	// This is the function we only want to call once you finish the sticky section
	async function loadDatabaseStuff() {
		if (dbLoaded) return;
		dbLoaded = true;

		console.log('Loading DB or performing some action AFTER scrolly is done...');
		// ... Place your DB code here ...
	}

	onMount(() => {
		// Set up an IntersectionObserver to watch the sentinel
		const observer = new IntersectionObserver((entries) => {
			entries.forEach((entry) => {
				// If the sentinel is intersecting (in view),
				// it means we've scrolled past the sticky section
				if (entry.isIntersecting) {
					loadDatabaseStuff();
					// Optionally stop observing if you only need this once
					observer.disconnect();
				}
			});
		}, {
			threshold: 0.1 // Adjust how far into view the sentinel must be
		});

		observer.observe(sentinel);
	});
</script>

<!-- YOUR STICKY/SCROLLY SECTION -->
<p>
  The following is a
  <a href="https://www.w3schools.com/howto/howto_css_sticky_element.asp">sticky chart</a>.
  As you scroll down, it'll stick to your window.
</p>

<div class="chart-container-scrolly" bind:clientWidth={width}>
  <Scatter 
    data={study}
    {value}
    {width}
    {height}
    {padding}
    {myNarrative} 
  />
</div>

<section id="scrolly">
  <h2>Scrolly <span>{value || "-"}</span></h2>
  <div class="spacer"></div>
  <Scrolly bind:value>
    {#each myNarrative as text, i}
      {@const active = value === i}
      <div class="step" class:active>
        <p>{text}</p>
      </div>
    {/each}
  </Scrolly>
  <div class="spacer"></div>
</section>

<!-- Our invisible sentinel: once it appears on screen,
     we know we’ve scrolled past the sticky chart. -->
<div bind:this={sentinel} style="height:1px"></div>

<p>Then we keep going after the sticky sequence. Below this line, your code that depends on the DB can appear, or you can show something once the DB loads, etc.</p>

<style>
	section,
	p {
		margin-top: 2rem;
		margin-bottom: 4rem;
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
		place-items: center; /* centers vertically */
		justify-content: center; /* centers horizontally */
		margin-left: -35%;
	}

	.step p {
		padding: 0.5rem 1rem;
		background: whitesmoke;
		color: #ccc;
		border-radius: 5px;
		box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
		transition: background 500ms ease;
	}

	.step.active p {
		background: white;
		color: black;
	}
</style>
