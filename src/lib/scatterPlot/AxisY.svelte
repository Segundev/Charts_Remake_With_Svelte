<script>
	export let yScale;
	export let width;
	export let margin;
	let yTicks = yScale.ticks(4);
</script>

<g transform="translate({0}, {margin.top})">
	{#each yTicks as tick}
		<g class="y-axis">
			<text class="text" x={0} y={yScale(tick)} dy="-4">
				{tick}%
			</text>
		</g>
		<g class="line">
			<line
				x1={0}
				y1={yScale(tick)}
				x2={width}
				y2={yScale(tick)}
				stroke={tick === 0 ? '#afafaf' : '#ccc'}
				stroke-width={tick === 0 ? 1.2 : 1}
				stroke-dasharray={tick === 0 ? 0 : 2}
			/>
		</g>
	{/each}
</g>

<style>
	.line {
		stroke-dasharray: 2;
	}
</style>
