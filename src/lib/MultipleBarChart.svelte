<script>
	import Background from '$lib/Background.svelte';

	//import data and axis components
	import data from '../lib/MultipleBarChart/data.json';
	import AxisX from '../lib/MultipleBarChart/AxisX.svelte';
	import AxisY from '../lib/MultipleBarChart/AxisY.svelte';

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

	const colorScale = scaleOrdinal().domain(subCategory).range(['#CC3366', '#69727D', '#225374']);

	let xData = (d) =>
		subCategory.map((key) => {
			return { key: key, value: d[key] };
		});
</script>

<Background>
	<div class="chart-container" bind:clientWidth={width}>
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
	</div>
</Background>

<style>
	:global(.text) {
		font-family: Helvetica, Arial;
		font-size: 0.75rem;
		font-weight: 200;
		fill: #999;
		text-anchor: start;
	}

	.chart-container {
		width: 100%;
		max-width: 550px;
		margin: 0 auto;
	}
	svg {
		width: 100%;
		height: 400px;
	}
</style>
