<script>
	import data from '$lib/DonutChart/data.js';
	import { scaleOrdinal } from 'd3-scale';
	import { schemeDark2 } from 'd3-scale-chromatic';
	import { pie, arc } from 'd3-shape';

	let width = 400;
	let height = 400;
	let margin = 40;

	let keys = Object.keys(data);
	const radius = Math.min(width, height) / 2 - margin;
	const color = scaleOrdinal().domain(keys).range(schemeDark2);

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

<div class="chartwrapper">
	<svg {width} {height}>
		<g transform="translate({width / 2}, {height / 2})">
			{#each data_ready as d}
				<path d={arcs(d)} fill={color(d.data[1])} opacity="0.9" />
			{/each}
			{#each data_ready as d}
				<polyline stroke="black" fill="none" stroke-width="1" points={labels(d)} />
			{/each}
			{#each data_ready as d}
				<text transform={labelText(d)} style="text-anchor={labelStyle(d)}"> {d.data[0]} </text>
			{/each}
		</g>
	</svg>
</div>
