<script>
	import Background from '$lib/Background.svelte';

	import data from '$lib/lineChart/data.json';
	import AxisX from '$lib/lineChart/AxisX.svelte';
	import AxisY from '$lib/lineChart/AxisY.svelte';

	import { scaleLinear } from 'd3-scale';
	import { extent, max } from 'd3-array';
	import { line, area } from 'd3-shape';

	let width = 400;
	let height = 300;
	const margin = { top: 80, right: 20, bottom: 20, left: 40 };
	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	$: xScale = scaleLinear()
		.domain(extent(data, (d) => d.x))
		.range([0, innerWidth]);

	const yScale = scaleLinear()
		.domain(extent(data, (d) => d.y))
		.range([innerHeight, 0]);

	$: xline = line()
		.x((d) => xScale(d.x))
		.y((d) => yScale(d.y));

	$: xArea = area()
		.x((d) => xScale(d.x))
		.y0((d) => yScale(d.CI_right))
		.y1((d) => yScale(d.CI_left));
</script>

<Background>
	<div class="chart-container" bind:clientWidth={width}>
		<h2>Line Chart</h2>
		<svg>
			<AxisX {xScale} {height} {width} {margin} />
			<AxisY {yScale} {width} {margin} />
			<g transform="translate({margin.left}, {margin.top})">
				<path fill="#fbf9eb" stroke="none" d={xArea(data)} />
				<path fill="none" stroke="#CF251E" stroke-width="2.5" d={xline(data)} />
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
		width: 100%;
		height: 300px;
	}
	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
