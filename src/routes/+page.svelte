<script>
	import '../style.css';
	import Chart from '$lib/Chart.svelte';
	import Navbar from '$lib/Navbar.svelte';
</script>

<div class="page-wrapper">
	<Navbar />
	<div class="chart-container">
		<header>
			<div>
				This project documents Charts I redeveloped as a learning process to learn d3 and svelte.
				Most of the data and chart were inspired and copied from d3-graph and Connor Rothshield.
			</div>
		</header>
		<div class="chart-wrapper">
			<Chart />
		</div>
	</div>
</div>

<style>
	.page-wrapper {
		display: grid;
		grid-template-columns: 20% 80%;
		/* flex-direction: row; */
	}

	.chart-container {
		grid-column: auto;
	}

	header div {
		padding: 1rem;
		background: var(--yellow);
		color: var(--black);
		font-weight: 500;
	}
</style>
