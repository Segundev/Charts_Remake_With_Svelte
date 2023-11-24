<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut, cubicIn } from 'svelte/easing';
	import { fade } from 'svelte/transition';

	import { max } from 'd3-array';
	import { area, curveNatural } from 'd3-shape';

	export let data;
	export let yAccessor;
	export let xAccessor;
	export let positionOnChart;
	export let width;
	export let height;
	export let xScale;
	export let yScale;
	export let formatTime;
	export let parseTime;

	let dataPoint = data.find((d) => xAccessor(d) === formatTime(xAccessor(positionOnChart)));
	console.log(Math.round(xScale(xAccessor(dataPoint))));
	const transitionDuration = 200;
	const tweenedTransition = {
		duration: transitionDuration,
		easing: cubicOut
	};

	//tweened coordinates
	let nearestDataX = tweened(Math.round(xScale(xAccessor(dataPoint))), tweenedTransition);
	let nearestDataY = tweened(yScale(yAccessor(dataPoint)), tweenedTransition);
	console.log(Math.round(xScale(xAccessor(dataPoint))));

	$: {
		dataPoint = data.find((d) => xAccessor(d) === formatTime(xAccessor(positionOnChart)));
		nearestDataX.set(xScale(parseTime(xAccessor(dataPoint))));
		nearestDataY.set(yScale(yAccessor(dataPoint)));
	}

	//-------------------------------------------------------------------
	// LAST DATA POINT
	//-------------------------------------------------------------------

	const lastDataPoint = data.find(
		(d) => xAccessor(d) === formatTime(max(data, (d) => parseTime(xAccessor(d))))
	);

	const lastDataPointCord = {
		x: xScale(parseTime(xAccessor(lastDataPoint))),
		y: yScale(yAccessor(lastDataPoint))
	};

	console.log(lastDataPointCord);

	//-------------------------------------------------------------------------
	// REFERENCE LINES COORDINATES IN PIXEL
	//-------------------------------------------------------------------------

	$: referenceLines = [
		{
			ref: 'nearestVert',
			x1: $nearestDataX,
			y1: $nearestDataY,
			x2: $nearestDataX,
			y2: yScale.range()[0] + 5
		},
		{
			ref: 'nearestHorz',
			x1: lastDataPointCord.x,
			y1: $nearestDataY,
			x2: xScale.range()[0],
			y2: $nearestDataY
		},
		{
			ref: 'lastVert',
			x1: lastDataPointCord.x,
			y1: lastDataPointCord.y,
			x2: lastDataPointCord.x,
			y2: yScale.range()[0] + 5
		},
		{
			ref: 'lastHorz',
			x1: lastDataPointCord.x,
			y1: lastDataPointCord.y,
			x2: xScale.range()[0],
			y2: lastDataPointCord.y
		}
	];

	//---------------------------------------------------
	// circles - coordinates in pixels
	//---------------------------------------------------

	$: circles = [{ ref: 'nearest', cx: $nearestDataX, cy: $nearestDataY }];

	//---------------------------------------------------------------
	// TEXT LABELS -- coordinates in pixels
	//---------------------------------------------------------------

	let textLabels = [];

	$: {
		textLabels = [
			{
				ref: 'x-nearest',
				text: formatTime(xScale.invert($nearestDataX)),
				x: $nearestDataX,
				y: yScale(0),
				dx: 0,
				dy: 21,
				textAnchor: 'middle',
				dominantBaseLine: 'auto',
				visible: true
			},
			{
				ref: 'x-last',
				text: formatTime(xScale.invert(lastDataPointCord.x)),
				x: lastDataPointCord.x,
				y: yScale(0),
				dx: 0,
				dy: 21,
				textAnchor: 'middle',
				dominantBaseLine: 'auto',
				visible: Math.abs(lastDataPointCord.x - $nearestDataX) < 30 ? false : true
			},
			{
				ref: 'y-nearest',
				text: yScale.invert($nearestDataY),
				x: xScale.range()[0],
				y: $nearestDataY,
				dx: -5,
				dy: 0,
				textAnchor: 'end',
				dominantBaseLine: 'middle',
				visible: true
			},
			{
				ref: 'y-last',
				text: yScale.invert(lastDataPointCord.y),
				x: xScale.range()[0],
				y: lastDataPointCord.y,
				dx: -5,
				dy: 0,
				textAnchor: 'end',
				dominantBaseLine: 'middle',
				visible: Math.abs(lastDataPointCord.y - $nearestDataY) < 20 ? false : true
			}
		];
	}

	//------------------------------------------------------------------------
	// THE ARROW
	//------------------------------------------------------------------------

	const minArrowLength = 30;

	$: showArrow = Math.abs($nearestDataY - lastDataPointCord.y) > minArrowLength;

	//------------------------------------------------------------------------
	// ARROW
	//------------------------------------------------------------------------

	$: areaGen = area()
		.curve(curveNatural)
		.x((d) => xScale(parseTime(xAccessor(d))))
		.y0(yScale(0))
		.y1((d) => yScale(yAccessor(d)))(data);

	// $: console.log(formatTime(xScale.invert($nearestDataX)))
</script>

<!-- Tooltip Group -->
<g transition:fade={{ duration: transitionDuration, easing: cubicIn }}>
	<!-- Reference Line -->
	<g class="reference-lines">
		{#each referenceLines as rl}
			<line class="reference-line" x1={rl.x1} y1={rl.y1} x2={rl.x2} y2={rl.y2} />
		{/each}
	</g>

	<!-- Text labels for crosshairs  -->
	<g class="text-labels">
		{#each textLabels as tl}
			{#if tl.visible}
				<text
					class="tt-reference-text"
					x={tl.x}
					y={tl.y}
					dx={tl.dx}
					dy={tl.dy}
					text-anchor={tl.textAnchor}
					dominant-baseline={tl.dominantBaseLine}
				>
					{tl.text}
				</text>
			{/if}
		{/each}
	</g>

	<path
		class="area"
		d={areaGen}
		fill="url(#tt-area-gradient)"
		stroke="#c94a54"
		stroke-width={3}
		opacity={1}
		mask="url(#hide-area)"
	/>

	<!-- circles -->
	<g class="circles">
		{#each circles as c}
			<circle cx={c.cx} cy={c.cy} r={6} />
		{/each}
	</g>

	<defs>
		<mask id="hide-area">
			<rect x="0" y="0" {width} {height} fill="white" />

			<!-- vertical mask -->
			<rect x={0} y={0} width={$nearestDataX} {height} fill="black" />
			<!-- horizontal mask -->
			<!-- orginally height={height-$nearestDataY} -->
			<!-- but this caused issues when exiting quickly on the right of the chart -->
			<!-- in that case the mask was the wrong shape -->
			<rect x={0} y={$nearestDataY} {width} {height} fill="black" />
		</mask>

		<linearGradient id="tt-area-gradient" x1="0" x2="0" y1="0" y2="1">
			<stop offset="0%" stop-color="#C94A54" />
			<stop offset="80%" stop-color="white" />
			<stop offset="100%" stop-color="white" />
		</linearGradient>
	</defs>
</g>

<style>
	.reference-line {
		stroke: #757373;
		stroke-dasharray: 5 3;
		pointer-events: none;
	}

	.tt-reference-text {
		pointer-events: none;
	}

	.area {
		transition: d all 500ms;
	}

	.circles {
		fill: white;
		stroke: #757373;
		stroke-width: 3;
		filter: drop-shadow(2px 2px 2px rgba(75, 73, 73, 75%));
	}
</style>
