<script>
	export let data;
	export let xScale;
	export let yScale;
	export let width;

	let tooltipWidth;
	let xNudge = 55;
	let yNudge = 70;

	$: x = xScale(data.Country);
	$: y = yScale(data.Rate);

	$: xPosition = x + tooltipWidth + xNudge > width ? x - tooltipWidth - xNudge : x + xNudge;
	$: yPosition = y + yNudge;
</script>

<div class="tooltip" style="left:{xPosition}px; top:{yPosition}px" bind:clientWidth={width}>
	<h1>{data.Country}</h1>
	<p>{Math.floor(data.Rate)}%</p>
</div>

<style>
	.tooltip {
		position: absolute;
		transition:
			top 300ms ease,
			left 300ms ease;
		background: #ffffff;
		border: 0.5px solid #dedede;
		border-radius: 4px;
		padding: 2px 4px;
		text-align: center;
	}

	.tooltip h1 {
		font-size: 0.9rem;
		font-weight: 900;
		color: #252525;
		padding: 4px 2px 2px 2px;
		border-bottom: 0.75px solid #dfdfdf;
	}

	.tooltip p {
		color: #cf251e;
		font-weight: 900;
		margin: 0;
	}
</style>
