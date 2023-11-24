<script>
	export let margin;
	export let xScale;
	export let height;
	export let hoveredEvent;

	import { timeParse, timeFormat } from 'd3-time-format';
	let parseTime = timeFormat('%b-%y');

	$: xTicks = xScale.ticks(6);
</script>

<g class="text axis-x" transform="translate({0} {height + margin.bottom})">
	{#if !hoveredEvent}
		{#each xTicks as year}
			<text x={xScale(year)} y={-20}> {parseTime(year)} </text>
		{/each}
	{/if}
</g>
