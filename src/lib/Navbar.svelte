<script>
	import Close from '$lib/Icons/Close.svelte';
	import Hamburger from '$lib/Icons/Hamburger.svelte';

	let isNavShowing = false;
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
				<li on:click={() => (isNavShowing = true)}><a href="#barchart">Bar Chart</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#columnchart">Column Chart</a></li>
				<li on:click={() => (isNavShowing = true)}>
					<a href="#stackedbarchart">Stacked Bar Chart</a>
				</li>
				<li on:click={() => (isNavShowing = true)}>
					<a href="#100percentbarchart">100 Percent Stacked Bar Chart</a>
				</li>
				<li on:click={() => (isNavShowing = true)}>
					<a href="#multiplebarchart">Multiple Bar Chart</a>
				</li>
				<li on:click={() => (isNavShowing = true)}><a href="#scatterplot">Scatter Plot</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#linechart">Line Chart</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#treemap">Tree map</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#donutchart">Donut Chart</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#africamap">Map of Africa</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#worldglobe">World Globe</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#mapbox">MapBox</a></li>
				<li on:click={() => (isNavShowing = true)}><a href="#scrolly">Scrollytelling</a></li>
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
