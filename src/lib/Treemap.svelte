<script>
	import data from '$lib/Treemap/data.js';
	import { stratify } from 'd3-hierarchy';
	import { treemap } from 'd3-hierarchy';
	import { scaleOrdinal } from 'd3-scale';
	import { schemeDark2 } from 'd3-scale-chromatic';

	let width = 450;
	let height = 450;
	const margin = { top: 20, right: 20, bottom: 20, left: 90 };

	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.right - margin.left;

	const color = scaleOrdinal().domain(['grp1', 'grp2', 'grp3', 'grp4', 'grp5']).range(schemeDark2);

	let root = stratify()
		.id((d) => d.name)
		.parentId((d) => d.parent)(data);
	root.sum((d) => +d.value);

	$: treemap().size([width, height]).padding(4)(root);

	console.log(root.leaves()[1]);
</script>

<div class="chartWrapper" bind:clientWidth={width}>
	<svg
		{width}
		height={height + margin.bottom + margin.top}
		transform="translate({margin.left}, {margin.top})"
	>
		{#each root.leaves() as d}
			<rect
				x={d.x0}
				y={d.y0}
				width={d.x1 - d.x0}
				height={d.y1 - d.y0}
				stroke="black"
				fill={color(d.id)}
				opacity="0.7"
			/>
		{/each}
		<g>
			{#each root.leaves() as d}
				<text x={d.x0 + 20} y={d.y0 + 30}> {d.data.name} </text>
			{/each}
		</g>
	</svg>
</div>
