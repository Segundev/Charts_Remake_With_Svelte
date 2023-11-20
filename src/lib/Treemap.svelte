<script>
	import Background from '$lib/Background.svelte';
	import data from '$lib/Treemap/data.js';
	import { stratify } from 'd3-hierarchy';
	import { treemap } from 'd3-hierarchy';
	import { scaleOrdinal } from 'd3-scale';

	let width = 400;
	let height = 400;
	const margin = { top: 20, right: 30, bottom: 20, left: 20 };

	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	const color = scaleOrdinal()
		.domain(['grp1', 'grp2', 'grp3', 'grp4', 'grp5'])
		.range(['#CF251E', '#f77f00', '#fcbf49', '#eae2b7', '#78290f']);

	let root = stratify()
		.id((d) => d.name)
		.parentId((d) => d.parent)(data);
	root.sum((d) => +d.value);

	$: root = treemap().size([innerWidth, innerHeight]).padding(4)(root);
</script>

<Background>
	<div id="treemap" class="chart-container" bind:clientWidth={width}>
		<h2>Tree map</h2>
		<svg {width} {height} transform="translate({margin.left}, {0})">
			{#each root.leaves() as d}
				<rect
					x={d.x0}
					y={d.y0}
					width={d.x1 - d.x0}
					height={d.y1 - d.y0}
					stroke="black"
					fill={color(d.id)}
				/>
			{/each}
			<g>
				{#each root.leaves() as d}
					<text x={d.x0 + 20} y={d.y0 + 30}> {d.data.name} </text>
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
		height: 400px;
	}

	text {
		font-size: 1rem;
		font-weight: 700;
		fill: var(--white);
	}
	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
