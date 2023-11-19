<script>
	export let xScale;
	export let height;
	export let margin;
	$: xTicks = xScale.ticks();
	$: console.log(xTicks);
</script>

<g transform="translate({margin.left}, 0)">
	{#each xTicks as tick}
		<text class="text" x={xScale(tick)} y={height} dx="0">
			{tick}
		</text>
	{/each}
</g>
