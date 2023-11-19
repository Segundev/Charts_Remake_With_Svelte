<script>
	export let xScale;
	export let height;
	export let margin;

	$: xTicks = xScale.ticks(6);
</script>

<g transform="translate({margin.left}, 0)">
	{#each xTicks as tick}
		<text class="text" x={xScale(tick)} y={height} dy={-margin.bottom}>
			{tick}%
		</text>
	{/each}
</g>
