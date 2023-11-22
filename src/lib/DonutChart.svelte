<script>
	import Background from '$lib/Background.svelte';

	import data from '$lib/DonutChart/data.js';
	import { scaleOrdinal } from 'd3-scale';
	import { pie, arc } from 'd3-shape';

	let width = 400;
	let height = 400;
	let margin = 40;

	let keys = Object.keys(data);
	const radius = Math.min(width, height) / 2 - margin;
	const color = scaleOrdinal()
		.domain(keys)
		.range(['#CF251E', '#f77f00', '#fcbf49', '#eae2b7', '#78290f']);

	const pies = pie()
		.sort(null)
		.value((d) => d[1]);
	const data_ready = pies(Object.entries(data));

	const arcs = arc()
		.innerRadius(radius * 0.5)
		.outerRadius(radius * 0.8);

	const outerArc = arc()
		.innerRadius(radius * 0.9)
		.outerRadius(radius * 0.9);

	function labels(d) {
		const posA = arcs.centroid(d);
		const posB = outerArc.centroid(d);
		const posC = outerArc.centroid(d);
		const midangle = d.startAngle + (d.endAngle - d.startAngle) / 2;
		posC[0] = radius * 0.95 * (midangle < Math.PI ? 1 : -1);
		return [posA, posB, posC];
	}

	function labelText(d) {
		const pos = outerArc.centroid(d);
		const midangle = d.startAngle + (d.endAngle - d.startAngle) / 2;
		pos[0] = radius * 0.99 * (midangle < Math.PI ? 1 : -1);
		return `translate(${pos})`;
	}

	function labelStyle(d) {
		const midangle = d.startAngle + (d.endAngle - d.startAngle) / 2;
		return midangle < Math.PI ? 'start' : 'end';
	}
</script>

<Background>
	<div id="donutchart" class="chart-container">
		<h2>Donut Chart</h2>
		<svg {width} {height}>
			<g transform="translate({width / 2}, {height / 2})">
				{#each data_ready as d}
					<path d={arcs(d)} fill={color(d.data[1])} opacity="0.9" />
				{/each}
				{#each data_ready as d}
					<polyline stroke="#969696" fill="none" stroke-width="1" points={labels(d)} />
				{/each}
				{#each data_ready as d}
					<text transform={labelText(d)} dy="-5" dx="5" style="text-anchor={labelStyle(d)}">
						{d.data[0]}
					</text>
				{/each}
			</g>
		</svg>
		<div class="footnote">
			<div class="source">
				Data Source: <a href="https://d3-graph-gallery.com/donut.html">D3-Graph</a>
			</div>
			<div class="code">
				Code: <a href="https://svelte.dev/repl/89fa47e1ff954a2082bbc68fb2b07c13?version=4.2.5"
					>Jayeola Gbenga</a
				>
			</div>
			<div class="inspiration">
				Inspiration: <a href="https://d3-graph-gallery.com/donut.html">D3-Graph</a>
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
		position: relative;
		width: 100%;
		height: 350px;
	}

	text {
		font-size: 1rem;
	}
	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
