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
	<div class="chart-container" bind:clientWidth={width}>
		<h1>Real GDP Growth Projections for 2023</h1>
		<p>
			Global GDP growth in 2023 is projected to be 2.7%, the lowest annual rate since the global
			financial crisis, with the exception of the 2020 pandemic period.
		</p>
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

	svg {
		width: 100%;
		height: 400px;
	}

	h1 {
		font-size: 20px;
		text-align: center;
		color: #525252;
	}

	p {
		font-family: Helvetica, Arial;
		font-size: 0.75rem;
		font-weight: 200;
		color: #999;
		text-align: center;
	}
</style>
