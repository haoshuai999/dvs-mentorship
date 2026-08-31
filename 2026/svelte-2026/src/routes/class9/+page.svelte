<script>
	import * as d3 from "d3";
	import { feature } from "topojson-client";
	import { onMount } from "svelte";

	// US state shapes (TopoJSON, US Census cartographic boundary, public domain).
	// Source: https://github.com/topojson/us-atlas
	// Raw file: https://cdn.jsdelivr.net/npm/us-atlas@3/states-10m.json
	//
	// GeoJSON vs TopoJSON:
	// GeoJSON is a plain JSON format where every feature stores its own full
	// coordinate list. Shared borders (e.g. the line between Colorado and Utah)
	// are duplicated in each neighboring state, which makes files larger and can
	// leave tiny gaps or overlaps between polygons.
	// TopoJSON is a topology-aware extension of GeoJSON: instead of raw polygons,
	// it stores a list of "arcs" (shared line segments) and references them by
	// index. Shared borders are stored exactly once, coordinates are quantized to
	// integers relative to a transform, and the result is typically 5-10x smaller
	// than the equivalent GeoJSON with cleaner topology. Because D3's geoPath
	// consumes GeoJSON, we use topojson-client's `feature()` to convert the
	// TopoJSON object back into GeoJSON features at render time.
	const TOPOJSON_URL =
		"https://cdn.jsdelivr.net/npm/us-atlas@3/states-10m.json";

	const width = 960;
	const height = 700;

	let states = $state([]);
	let values = $state({});
	let hovered = $state(null);

	onMount(() => {
		d3.json(TOPOJSON_URL).then((topo) => {
			states = feature(topo, topo.objects.states).features;
			values = Object.fromEntries(
				states.map((f) => [f.properties.name, Math.floor(Math.random() * 101)]),
			);
		});
	});

	const projection = $derived(
		d3.geoAlbersUsa().fitSize([width, height], {
			type: "FeatureCollection",
			features: states,
		}),
	);

	const pathGen = $derived(d3.geoPath(projection));

	const color = d3
		.scaleSequential()
		.domain([0, 100])
		.interpolator(d3.interpolateReds);

	const legendWidth = 260;
	const legendHeight = 10;
	const legendX = 40;
	const legendY = 20;

	const legendTickScale = d3.scaleLinear([0, 100], [0, legendWidth]);
	const legendTicks = color.ticks();
</script>

<h1>Class 9 - D3 US State Map</h1>

<p>
	Hover a state to see its dummy value. Shape data: US Census cartographic
	boundary via
	<a
		href="https://github.com/topojson/us-atlas"
		target="_blank"
		rel="noreferrer"
	>
		us-atlas
	</a>
	.
</p>

<div class="chart-wrap">
	<div class="info">
		{#if hovered}
			<strong>{hovered.name}</strong>: {hovered.value}
		{:else}
			<em>Hover a state</em>
		{/if}
	</div>

	<svg {width} {height} aria-label="US state map">
		<g transform="translate(0, 30)">
			{#each states as state (state.properties.name)}
				{@const name = state.properties.name}
				{@const value = values[name]}
				<path
					d={pathGen(state)}
					fill={color(value)}
					stroke="#ffffff"
					stroke-width="0.5"
					class:hovered={hovered?.name === name}
					onmouseenter={() => (hovered = { name, value })}
					onmouseleave={() => (hovered = null)}
					role="img"
					aria-label={`${name}: ${value}`}
				>
					<title>{name}: {value}</title>
				</path>
			{/each}
		</g>

		<g transform={`translate(${legendX}, ${legendY})`} class="legend">
			{#each legendTicks as t, i}
				{#if i < legendTicks.length - 1}
					<rect
						x={legendTickScale(t)}
						width={legendTickScale(legendTicks[i + 1]) - legendTickScale(t)}
						height={legendHeight}
						fill={color((t + legendTicks[i + 1]) / 2)}
					/>
				{/if}
				<line
					x1={legendTickScale(t)}
					x2={legendTickScale(t)}
					y1={legendHeight}
					y2={legendHeight + 4}
					stroke="#333"
				/>
				<text
					x={legendTickScale(t)}
					y={legendHeight + 6}
					text-anchor="middle"
					dominant-baseline="hanging"
				>
					{t}
				</text>
			{/each}
		</g>
	</svg>
</div>

<style>
	.chart-wrap {
		display: flex;
		flex-direction: column;
		gap: 8px;
	}

	path.hovered {
		stroke: #111;
		stroke-width: 1;
	}

	.info {
		font-size: 14px;
		min-height: 1.2em;
	}

	.legend text {
		font-size: 11px;
		fill: #333;
	}
</style>
