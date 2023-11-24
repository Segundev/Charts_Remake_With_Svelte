<script>
	import Background from '$lib/Background.svelte';

	// import data
	import data from '$lib/AreaChart/data.json';
	import { minx, maxx } from '$lib/AreaChart/settings.js';

	// import other chart peripherals components
	import AxisX from '$lib/AreaChart/AxisX.svelte';
	import AxisY from '$lib/AreaChart/AxisY.svelte';
	import Tooltip from '$lib/AreaChart/Tooltip.svelte';
	import Mini from '$lib/AreaChart/Mini.svelte';

	// import d3 module - delete if not needed
	import * as d3 from 'd3';
	import { timeParse, utcParse, timeFormat } from 'd3-time-format';

	// import d3 dependencies
	import { scaleLinear, scaleOrdinal, scaleTime } from 'd3-scale';
	import { min, max, extent } from 'd3-array';

	// -------------------------------------------------------------
	// ACCESSORS
	// -------------------------------------------------------------
	const xAccessor = (d) => d.ItemLabels;
	const yAccessor = (d) => +d.wheatFlour;

	let minX, maxX;
	$: minX = data[0].ItemLabels;
	$: maxX = data[data.length - 1].ItemLabels;

	minx.subscribe((value) => {
		minX = value;
	});

	maxx.subscribe((value) => {
		maxX = value;
	});

	// create margins, chart boundaries and dimensions
	let margin = { top: 25, bottom: 25, left: 25, right: 25 };
	let height = 300;
	let width = 400;

	let innerHeight = height - margin.top - margin.bottom;
	$: innerWidth = width - margin.left - margin.right;

	$: filteredData = data.filter(
		(d) => parseTime(xAccessor(d)) >= parseTime(minX) && parseTime(xAccessor(d)) <= parseTime(maxX)
	);

	let parseTime = timeParse('%y-%b');
	let parseutc = utcParse('%y-%b');
	let formatTime = timeFormat('%y-%b');

	$: xScale = scaleTime()
		.domain([parseTime(minX), parseTime(maxX)])
		.range([margin.left, innerWidth]);

	$: yScale = scaleLinear()
		.domain([0, max(data, (d) => yAccessor(d))])
		.range([innerHeight, margin.bottom]);

	// Add line to path
	$: line = d3
		.line()
		.x((d) => xScale(parseTime(xAccessor(d))))
		.y((d) => yScale(yAccessor(d)))
		.curve(d3.curveCatmullRom);

	$: areaPath = d3
		.area()
		.x((d) => xScale(parseTime(xAccessor(d))))
		.y0(yScale(margin.bottom + margin.top))
		.y1((d) => yScale(yAccessor(d)));

	let hovered;

	let hoveredEvent = null;
	let mousePosition = {};
	let positionOnChart = {};

	// -------------------------------------------------------------------
	//  Encountered a blocker trying to get the data from
	//  the mouse position. the value from  xScale.invert(mousePosition.x)
	//	 is close to domain value when hardcoded so no need to subtract from
	//	 margin.left. But the values are not the same in order to match for equality
	//  for data.find to fetch the corresponding data
	//--------------------------------------------------------------------

	$: {
		// calculate mouse position from event
		if (hoveredEvent) {
			mousePosition.x = hoveredEvent.layerX - margin.left;
			mousePosition.y = hoveredEvent.layerY - margin.top;

			// is mouse within chart but outside the coverage of the data
			let nearestYear = xScale.invert(mousePosition.x);
			let minYear = min(data, (d) => parseTime(xAccessor(d)));
			nearestYear = nearestYear >= minYear ? nearestYear : minYear;

			positionOnChart = {
				ItemLabels: nearestYear,
				wheatFlour: yAccessor(data.find((d) => d.ItemLabels === formatTime(nearestYear)))
			};
		}
	}
</script>

<Background>
	<div id="areachart" class="chart-container">
		<h2>Area Chart</h2>
		<!-- CHART COMPONENTS -->
		<div bind:clientWidth={width} class="svg-container" on:mouseleave={() => (hovered = null)}>
			<svg {width} {height}>
				<g class="inner-chart" transform="translate({margin.left} {margin.top})">
					<AxisX {xScale} height={innerHeight} {margin} {hoveredEvent} />
					<AxisY {yScale} width={innerWidth} {hovered} />
					<g class="line">
						<path
							fill="none"
							stroke="#CF251E"
							stroke-width="3"
							stroke-dasharray="0"
							d={line(filteredData)}
						/>
						<path d={areaPath(filteredData)} fill="url(#area-gradient" opacity="0.3" />
					</g>
					<defs>
						<linearGradient id="area-gradient" x1="0" x2="0" y1="0" y2="1">
							<stop offset="0%" stop-color="#D19499"></stop>
							<stop offset="100%" stop-color="#F8F1F4"></stop>
						</linearGradient>
					</defs>
					{#if hoveredEvent}
						<Tooltip
							{positionOnChart}
							{xScale}
							{yScale}
							{data}
							{formatTime}
							{parseTime}
							{xAccessor}
							{yAccessor}
							width={innerWidth}
							height={innerHeight}
						/>
					{/if}
					<rect
						id="listener-layer"
						x={0}
						y={0}
						width={innerWidth}
						height={innerHeight}
						fill="transparent"
						on:mouseover={(e) => (hoveredEvent = e)}
						on:focus={(e) => (hoveredEvent = e)}
						on:mousemove={(e) => (hoveredEvent = e)}
						on:mouseleave={() => (hoveredEvent = null)}
						on:mouseout={() => (hoveredEvent = null)}
						on:blur={() => (hoveredEvent = null)}
						on:touchend={() => (hoveredEvent = null)}
					/>
				</g>
			</svg>
		</div>
	</div>

	<footer>
		<Mini {margin} {innerWidth} {minX} {maxX} />
	</footer>

	<div class="footnote">
		<div class="source">
			Data Source: <a href="https://svelte.dev/repl/ffb17e011c5542ddbd131fd32b3449d4?version=4.2.7"
				>Jayeola Gbenga</a
			>
		</div>
		<div class="code">
			Code: <a href="https://svelte.dev/repl/ffb17e011c5542ddbd131fd32b3449d4?version=4.2.7"
				>Jayeola Gbenga</a
			>
		</div>
		<div class="inspiration">
			Inspiration: <a href="#">Chris Martin</a>
		</div>
	</div>
</Background>

<style>
	.chart-container {
		width: 100%;
		max-width: 700px;
		margin: 0 auto;
		/* border: 1px solid #999; */
	}

	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
