<script>
	export let yScale;
	export let width;
	export let hovered;

	let yTicks = yScale.ticks(4);
</script>

<g class="axis-y">
	{#each yTicks as tick}
		<line
			x1={0}
			x2={width}
			y1={yScale(tick) - 10}
			y2={yScale(tick) - 10}
			stroke="#ddd"
			stroke-width="1"
		/>
	{/each}
	{#if !hovered}
		{#each yTicks as tick}
			<text class="text" x={0} y={yScale(tick)} dy="-15"> {tick}</text>
		{/each}
	{:else}
		<text class="text" x={0} y={yScale(yTicks[0])} dy="-15"> {yTicks[0]}</text>
	{/if}
</g>
