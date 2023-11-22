<script>
	import Background from '$lib/Background.svelte';

	import data from '$lib/ColumnChart/Data.json';
	import AxisX from '$lib/ColumnChart/AxisX.svelte';
	import AxisY from '$lib/ColumnChart/AxisY.svelte';

	import { scaleLinear } from 'd3-scale';
	import { scaleBand } from 'd3-scale';
	import { max } from 'd3-array';

	let width = 400;
	let height = 400;
	const margin = { top: 20, right: 20, bottom: 20, left: 90 };

	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;
	const countries = data.map((d) => d.Country);

	data.sort((a, b) => {
		return b.Rate - a.Rate;
	});

	$: xScale = scaleLinear()
		.domain([0, max(data, (d) => d.Rate)])
		.range([0, innerWidth]);
	const yScale = scaleBand().domain(countries).range([0, innerHeight]).paddingInner(0.3);
</script>

<Background>
	<div id="columnchart" bind:clientWidth={width}>
		<h2>Column Chart</h2>
		<svg>
			<AxisX {xScale} {margin} {height} />
			<AxisY {yScale} {margin} {width} />
			<g transform="translate({margin.left}, 0)">
				{#each data as d}
					<rect
						x={0}
						y={yScale(d.Country)}
						width={xScale(d.Rate) - xScale(0)}
						height={yScale.bandwidth()}
						fill="#CF251E"
					/>
				{/each}
			</g>
		</svg>
		<div class="footnote">
			<div class="source">
				Data Source: <a href="https://d3-graph-gallery.com/graph/barplot_horizontal.html"
					>D3-Graph</a
				>
			</div>
			<div class="code">
				Code: <a href="https://svelte.dev/repl/3e9a97c889c34a2a8c0278f8104be44d?version=4.2.5"
					>Jayeola Gbenga</a
				>
			</div>
			<div class="inspiration">
				Inspiration: <a href="https://d3-graph-gallery.com/graph/barplot_horizontal.html"
					>D3-Graph</a
				>
			</div>
		</div>
	</div>
</Background>

<style>
	svg {
		width: 100%;
		height: 400px;
	}

	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
