<script>
	export let xScale;
	export let height;
	export let width;
	export let margin;
	$: xTicks = xScale.domain();
</script>

<g transform="translate({margin.left}, 0)">
	{#each xTicks as tick}
		<text class="text" x={xScale(tick)} y={height} transform="rotate(0)">
			{width < 600 && tick.length > 4 ? tick.slice(0, 4) : tick}
		</text>
	{/each}
</g>
