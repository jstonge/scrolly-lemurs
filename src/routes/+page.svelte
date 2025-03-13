<script>
	import { setContext } from "svelte";
	import { browser } from "$app/environment";
	import Index from "$components/Index.svelte";
	// import copy from "$data/copy.json";
	// import version from "$utils/version.js";

	let { data } = $props();


	// const { title, description, url, keywords } = copy;
	// setContext("copy", copy);
	setContext("data", data);
</script>

<Index />