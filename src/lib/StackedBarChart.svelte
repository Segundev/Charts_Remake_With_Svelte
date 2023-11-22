<script>
	import Background from '$lib/Background.svelte';

	//import data and axis components
	import data from '$lib/PercentStacked/data.json';
	import AxisX from '$lib/StackedBarChart/AxisX.svelte';
	import AxisY from '$lib/StackedBarChart/AxisY.svelte';

	// import d3 dependencies
	import { scaleLinear, scaleOrdinal } from 'd3-scale';
	import { scaleBand } from 'd3-scale';
	import { stack } from 'd3-shape';
	import { map } from 'd3-array';

	let width = 400;
	let height = 300;
	const margin = { top: 20, right: 20, bottom: 20, left: 40 };
	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	const category = map(data, (d) => d.group);
	const subCategory = Object.keys(data[0]).slice(1);

	$: xScale = scaleBand().domain(category).range([0, innerWidth]).paddingInner(0.3);

	const yScale = scaleLinear().domain([0, 60]).range([innerHeight, 0]);

	const colorScale = scaleOrdinal().domain(subCategory).range(['#CF251E', '#f77f00', '#fcbf49']);

	const stackData = stack().keys(subCategory)(data);
</script>

<Background>
	<div id="stackedbarchart" class="chart-container" bind:clientWidth={width}>
		<h2>Stacked Bar Chart</h2>
		<svg>
			<AxisX {xScale} {height} {margin} />
			<AxisY {yScale} {width} {margin} />
			<g transform="translate({margin.left}, {margin.top})">
				{#each stackData as datum}
					{#each datum as d}
						<rect
							x={xScale(d.data.group)}
							y={yScale(d[1])}
							width={xScale.bandwidth()}
							height={yScale(d[0]) - yScale(d[1])}
							fill={colorScale(datum.key)}
						/>
					{/each}
				{/each}
			</g>
		</svg>
		<div class="footnote">
			<div class="source">
				Data Source: <a href="https://d3-graph-gallery.com/barplot.html">D3-Graph</a>
			</div>
			<div class="code">
				Code: <a href="https://svelte.dev/repl/ef2e0cc9c98e4c33923716c90c74949b?version=4.2.5"
					>Jayeola Gbenga</a
				>
			</div>
			<div class="inspiration">
				Inspiration: <a href="https://d3-graph-gallery.com/barplot.html">D3-Graph</a>
			</div>
		</div>
	</div>
</Background>

<style>
	.chart-container {
		width: 100%;
		max-width: 700px;
		margin: 0 auto;
	}
	svg {
		width: 100%;
		height: 300px;
	}

	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
