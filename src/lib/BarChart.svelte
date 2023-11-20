<script>
	import Background from '$lib/Background.svelte';

	//import data and axis components
	import data from '$lib/BarChart/Data.json';
	import AxisX from '$lib/BarChart/AxisX.svelte';
	import AxisY from '$lib/BarChart/AxisY.svelte';
	import Tooltip from '$lib/BarChart/Tooltip.svelte';

	// import d3 dependencies
	import { scaleLinear } from 'd3-scale';
	import { scaleBand } from 'd3-scale';
	import { max } from 'd3-array';

	let width = 400;
	let height = 300;
	const margin = { top: 20, right: 20, bottom: 20, left: 30 };

	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;
	const countries = data.map((d) => d.Country);

	data.sort((b, a) => {
		return a.Rate - b.Rate;
	});

	$: xScale = scaleBand().domain(countries).range([0, innerWidth]).paddingInner(0.2);
	let yScale = scaleLinear()
		.domain([0, max(data, (d) => +d.Rate)])
		.range([innerHeight, 0]);

	let hovered;
</script>

<Background>
	<div
		class="chart-container"
		bind:clientWidth={width}
		role="figure"
		on:mouseleave={() => (hovered = null)}
	>
		<h2>Bar Chart</h2>
		<svg>
			<AxisX {xScale} {height} {width} {margin} />
			<AxisY {yScale} {width} {margin} />
			<g class="inner-chart" transform="translate({margin.left}, {margin.top})">
				{#each data as d}
					<rect
						x={xScale(d.Country)}
						y={yScale(d.Rate)}
						width={xScale.bandwidth()}
						height={yScale(0) - yScale(d.Rate)}
						fill={hovered === d ? '#8E0F0A' : '#CF251E'}
						role="list"
						on:mouseover={() => (hovered = d)}
						on:focus={() => (hovered = d)}
					/>
				{/each}
			</g>
		</svg>

		<div class="footnote">
			<div class="source">Data Source: <a href="#">D3-Graph</a></div>
			<div class="code">Code: <a href="#">Jayeola Gbenga</a></div>
			<div class="inspiration">Inspiration: <a href="#">D3-Graph</a></div>
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
		position: relative;
		width: 100%;
		height: 300px;
	}
	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
