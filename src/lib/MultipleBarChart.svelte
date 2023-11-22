<script>
	import Background from '$lib/Background.svelte';

	//import data and axis components
	import data from '$lib/MultipleBarChart/data.json';
	import AxisX from '$lib/MultipleBarChart/AxisX.svelte';
	import AxisY from '$lib/MultipleBarChart/AxisY.svelte';

	// import d3 dependencies
	import { scaleLinear, scaleOrdinal } from 'd3-scale';
	import { scaleBand } from 'd3-scale';
	import { map, max } from 'd3-array';

	let width = 400;
	let height = 300;
	const margin = { top: 20, right: 20, bottom: 20, left: 40 };
	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	const category = map(data, (d) => d.group);
	const subCategory = Object.keys(data[0]).slice(1);

	$: xScale = scaleBand().domain(category).range([0, innerWidth]).paddingInner(0.3);

	$: xSubgroup = scaleBand().domain(subCategory).range([0, xScale.bandwidth()]).paddingInner(0.09);

	const yScale = scaleLinear()
		.domain([0, max(data, (d) => d.stress)])
		.range([innerHeight, 0]);

	const colorScale = scaleOrdinal().domain(subCategory).range(['#CF251E', '#f77f00', '#fcbf49']);

	let xData = (d) =>
		subCategory.map((key) => {
			return { key: key, value: d[key] };
		});
</script>

<Background>
	<div id="multiplebarchart" class="chart-container" bind:clientWidth={width}>
		<h2>Multiple Bar Chart</h2>
		<svg>
			<AxisX {xScale} {height} {width} {margin} />
			<AxisY {yScale} {width} {margin} />
			<g transform="translate({margin.left}, {margin.top})">
				{#each data as datum, i}
					<g transform="translate({xScale(datum.group)},0)">
						{#each xData(datum) as d}
							<rect
								x={xSubgroup(d.key)}
								y={yScale(d.value)}
								width={xSubgroup.bandwidth()}
								height={innerHeight - yScale(d.value)}
								fill={colorScale(d.key)}
							/>
						{/each}
					</g>
				{/each}
			</g>
		</svg>
		<div class="footnote">
			<div class="source">
				Data Source: <a href="https://d3-graph-gallery.com/barplot.html">D3-Graph</a>
			</div>
			<div class="code">
				Code: <a href="https://svelte.dev/repl/ec7041aa7b0d4f52bd2314950d688842?version=4.2.5"
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
