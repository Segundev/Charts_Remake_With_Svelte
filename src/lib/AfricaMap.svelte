<script>
	import Background from '$lib/Background.svelte';

	import world from '$lib/AfricaMap/MapData.json';
	import * as topojson from 'topojson-client';
	import * as topojsonServer from 'topojson-server';
	import { geoCentroid, geoPath } from 'd3-geo';
	import { geoChamberlinAfrica } from 'd3-geo-projection';
	import { scaleOrdinal } from 'd3-scale';

	let width = 400;
	let height = 550;

	topojson.merge(world, world.objects.countries.geometries);
	let africanCountries = topojson.feature(world, world.objects.countries).features.filter((d) => {
		const c = geoCentroid(d);
		return c[0] > -20 && c[0] < 47 && c[1] < 34.5 && c[0] + c[1] < 60;
	});

	let Africa0 = topojsonServer.topology({
		countries: { type: 'FeatureCollection', features: africanCountries }
	});
	let countries = [
		'Morocco',
		'Tunisia',
		'Egypt',
		'Somalia',
		'Kenya',
		'Dem. Rep. Congo',
		'Nigeria',
		'Zambia',
		'Namibia',
		'Tanzania',
		'Botswana',
		'South Africa'
	];

	let colorScale = scaleOrdinal()
		.domain(countries)
		.range(['#CF251E', '#f77f00', '#fcbf49', '#eae2b7', '#78290f']);

	let Africa = topojson.merge(Africa0, Africa0.objects.countries.geometries);

	$: projection = geoChamberlinAfrica()
		.fitExtent(
			[
				[50, 50],
				[width - 50, height - 50]
			],
			Africa
		)
		.clipExtent([
			[-2, -2],
			[width + 2, height + 2]
		]);

	$: path = geoPath(projection);
</script>

<Background>
	<div id="africamap" class="chart-container" bind:clientWidth={width} role="figure" tabindex="0">
		<h2>Map of Africa</h2>
		<svg {width} {height} g transform="translate(0, 0)">
			<title> Map of Africa showing key recipient countries </title>
			<g id="all">
				<path
					id="land"
					fill="#fcfcfc"
					stroke="none"
					d={path(topojson.feature(world, world.objects.land))}
				/>
				<path
					id="borders-all"
					fill="none"
					stroke="#eee"
					d={path(topojson.mesh(world, world.objects.countries, (a, b) => a !== b))}
				/>
				<path
					id="shore-all"
					fill="none"
					stroke="#ddd"
					d={path(topojson.feature(world, world.objects.land))}
				/>
			</g>
			<g id="africa">
				<g id="countries-africa">
					{#each africanCountries as country}
						<path
							fill={countries.includes(country.properties.name)
								? colorScale(country.properties.name)
								: '#d9d9d9'}
							stroke="none"
							d={path(country)}
						/>
					{/each}
					<path
						id="borders-africa"
						fill="none"
						stroke="none"
						d={path(topojson.mesh(Africa0, Africa0.objects.countries, (a, b) => a !== b))}
					/>
					<path id="shore-africa" fill="none" stroke="#ddd" d={path(Africa)} />
				</g>
			</g>
		</svg>
		<div class="footnote">
			<div class="source">
				Data Source: <a href="https://d3-graph-gallery.com/barplot.html">D3-Graph</a>
			</div>
			<div class="code">
				Code: <a href="https://svelte.dev/repl/4f1b8615ec074c319c4595ce98fc04bd?version=4"
					>Jayeola Gbenga</a
				>
			</div>
			<div class="inspiration">
				Inspiration: <a href="https://d3-graph-gallery.com/barplot.html">D3-Graph</a>
			</div>
		</div>
	</div>
</Background>

<style>
	.chart-container {
		width: 100%;
		max-width: 700px;
		margin: 0 auto;
	}

	svg {
		position: relative;
		width: 100%;
		height: 100%;
	}
	a {
		color: var(--grey);
		font-size: 0.75rem;
	}
</style>
