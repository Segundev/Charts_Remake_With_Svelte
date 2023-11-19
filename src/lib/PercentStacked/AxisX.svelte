<script>
	export let xScale;
	export let height;
	export let margin;
	$: xTicks = xScale.domain();
</script>

<g transform="translate({margin.left + margin.right}, 0)">
	{#each xTicks as tick}
		<text class="text" x={xScale(tick)} y={height} dx="-10" text-anchor="middle">
			{tick}
		</text>
	{/each}
</g>
