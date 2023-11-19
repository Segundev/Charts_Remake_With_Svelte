<script>
	import Background from '$lib/Background.svelte';

	import data from '$lib/scatterPlot/data.json';
	import AxisX from '$lib/scatterPlot/AxisX.svelte';
	import AxisY from '$lib/scatterPlot/AxisY.svelte';

	import { scaleLinear, scaleOrdinal, scaleSqrt } from 'd3-scale';
	import { extent, map, max } from 'd3-array';

	import { schemeAccent } from 'd3-scale-chromatic';

	let width = 400;
	let height = 300;
	const margin = { top: 20, right: 20, bottom: 20, left: 40 };
	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	$: xScale = scaleLinear()
		.domain(extent(data, (d) => d.gdpPercap))
		.range([0, innerWidth]);

	const yScale = scaleLinear()
		.domain(extent(data, (d) => d.lifeExp))
		.range([innerHeight, 0]);

	const radiusScale = scaleSqrt()
		.domain(extent(data, (d) => d.pop))
		.range([4, 30]);

	const colorScale = scaleOrdinal()
		.domain(['Asia', 'Europe', 'Americas', 'Africa', 'Oceania'])
		.range(schemeAccent);
</script>

<Background>
	<div class="chart-container" bind:clientWidth={width}>
		<svg>
			<AxisX {xScale} {height} {width} {margin} />
			<AxisY {yScale} {width} {margin} />
			<g transform="translate({margin.left}, {margin.top})">
				{#each data as d}
					<circle
						cx={xScale(d.gdpPercap)}
						cy={yScale(d.lifeExp)}
						r={radiusScale(d.pop)}
						fill={colorScale(d.continent)}
						stroke="#ccc"
						opacity="0.8"
					/>
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
		height: 350px;
	}
</style>
