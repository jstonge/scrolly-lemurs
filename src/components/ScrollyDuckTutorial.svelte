<script>
	import { instantiateDuckDb } from '$lib/duckdb';
	import { base } from '$app/paths';
	import Speech from '$components/speech.svelte';
	import Spinner from '$components/Spinner.svelte';

	// Load and initialize the database
	async function loadDatabase() {
		console.log('LOADING DB');
		const db = await instantiateDuckDb();

		// Register the Parquet file
		await db.registerFileURL('SOTU.parquet', `${base}/SOTU.parquet`, 4, false);

		// Connect and create a convenience table
		const conn = await db.connect();
		await conn.query(`
			CREATE TABLE p1 AS 
			SELECT * 
			FROM parquet_scan('SOTU.parquet')
		`);

		return conn;
	}

	// Start loading the DB right away
	const dbConnectionPromise = loadDatabase();

	// Generate a range of years (1934–2020)
	let years = [];
	for (let y = 1934; y < 2021; y++) {
		years.push(y);
	}

	// Use a promise for query results so we can await it in the template
	$: results = new Promise(() => {});

	// Fetch SOTU data for a given year
	async function fetchYear(year) {
		const conn = await dbConnectionPromise;
		results = conn.query(`
			SELECT *
			FROM parquet_scan('SOTU.parquet')
			WHERE year == ${year}
		`);
	}

	// Load an initial year when the DB is ready
	dbConnectionPromise.then(() => fetchYear(1936));
</script>

<h1>Hello duckDB!</h1>

<div class="description">
	<p>
		This is a demo of a static, database-driven page using DuckDB WASM (based on <a href="https://github.com/duckdb-wasm-examples/sveltekit-typescript/tree/main">this repo</a>). All the primary text is stored
		in a Parquet file structured according to the <a href="https://nonconsumptive.org" target="_blank" rel="noopener noreferrer">nonconsumptive
		</a> text sharing spec.
	</p>
	<p>
		On load, the page fetches DuckDB in WASM form, then mounts the Parquet file into the database. 
		Click any year below to load that State of the Union address.
	</p>
</div>

<div class="years">
	{#each years as year}
		<div class="year" on:click={() => fetchYear(year)}>
			{year}
		</div>
	{/each}
</div>

{#await results}
	<Spinner />
{:then result}
<Speech {results} />
{/await}

<style>
	section,
	p {
		margin-top: 2rem;
		margin-bottom: 4rem;
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
	
	
	.description {
		max-width: 700px;
		margin: 1rem auto;
		font-size: 1rem;
		line-height: 1.5;
	}

	.years {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		max-width: 700px;
		margin: 1rem auto;
	}

	.year {
		cursor: pointer;
		padding: 0.25rem 0.5rem;
		border: 1px solid #333;
		margin: 0.25rem;
		transition: background-color 0.2s ease;
	}

	.year:hover {
		background-color: whitesmoke;
	}
</style>
