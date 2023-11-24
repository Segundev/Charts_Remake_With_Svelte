<script>
	import Close from '$lib/Icons/Close.svelte';
	import Hamburger from '$lib/Icons/Hamburger.svelte';

	let isNavShowing = false;
	const chartType = [
		{ label: 'Bar Chart', id: '#barchart' },
		{ label: 'Column Chart', id: '#columnchart' },
		{ label: 'Stacked Bar Chart', id: '#stackedbarchart' },
		{ label: '100 Percent Stacked Bar Chart', id: '#100percentbarchart' },
		{ label: 'Multiple Bar Chart', id: '#multiplebarchart' },
		{ label: 'Scatter Plot', id: '#scatterplot' },
		{ label: 'Line Chart', id: '#linechart' },
		{ label: 'Tree Map', id: '#treemap' },
		{ label: 'Donut Chart', id: '#donutchart' },
		{ label: 'Area Chart', id: '#areachart' },
		{ label: 'Map of Africa', id: '#africamap' },
		{ label: 'World Globe', id: '#worldglobe' },
		{ label: 'MapBox', id: '#mapbox' },
		{ label: 'Scrollytelling', id: '#scrolly' }
	];
</script>

<button on:click={() => (isNavShowing = !isNavShowing)}>
	{#if isNavShowing}
		<Close />
	{:else}
		<Hamburger />
	{/if}
</button>
<div class="sidebar" class:isNavShowing>
	<div class="chart-items">
		<div class="title">Charts</div>
		<div class="chart-list">
			<ol>
				{#each chartType as chart}
					<li on:click={() => (isNavShowing = true)}>
						<a href={chart.id}>{chart.label}</a>
					</li>
				{/each}
			</ol>
		</div>
	</div>
</div>

<style>
	.sidebar {
		margin: 0;
		padding: 0;
		background-color: var(--blue);
		width: 30%;
		position: fixed;
		height: 100%;
		overflow-y: hidden;
	}

	.title {
		font-size: 1.75rem;
		font-weight: 900;
		color: var(--white);
		padding: 1rem;
		padding-left: 2.5rem;
	}

	ol li {
		list-style-type: none;
		padding-bottom: 1rem;
	}

	button {
		display: none;
		position: fixed;
		top: 2rem;
		right: 2rem;
		background: none;
		color: inherit;
		border: none;
		padding: 0;
		font: inherit;
		cursor: pointer;
		outline: inherit;
		z-index: 99;
	}

	@media (width < 650px) {
		.sidebar {
			width: 100%;
			z-index: 1;
		}

		.chart-items {
			padding-top: 1.75rem;
			text-align: center;
		}

		.title {
			padding: 0;
		}

		ol {
			padding: 20px;
		}

		.isNavShowing {
			transform: translateX(-100%);
		}

		button {
			display: block;
		}
	}
</style>
