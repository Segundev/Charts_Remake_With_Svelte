<script>
	export let yScale;
	export let width;
	export let margin;
	let yTicks = yScale.domain();
</script>

<g transform="translate({0}, {margin.top})">
	{#each yTicks as tick, i}
		<g class="y-axis">
			<text class="text" x={0} y={yScale(tick)} dy="-2">
				{tick}
			</text>
		</g>
		<g class="line">
			<line
				x1={0}
				y1={yScale(tick) + 10}
				x2={width}
				y2={yScale(tick) + 10}
				stroke="#ccc"
				stroke-width={i === 9 ? 0 : 1}
				stroke-dasharray={i === 9 ? 0 : 2}
			/>
		</g>
	{/each}
</g>

<style>
	.line {
		stroke-dasharray: 2;
	}
</style>
