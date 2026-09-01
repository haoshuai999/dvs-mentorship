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

	let usStates = $state([]);
	let values = $state({});
	let hovered = $state(null);

	onMount(() => {
		d3.json(TOPOJSON_URL).then((topology) => {
			usStates = feature(topology, topology.objects.states).features;
			console.log(usStates);
			values = Object.fromEntries(
				usStates.map((d) => [d.properties.name, Math.random() * 100]),
			);
			console.log(values);
		});
	});

	const projection = $derived(
		d3.geoAlbersUsa().fitSize([width, height], {
			type: "FeatureCollection",
			features: usStates,
		}),
	);

	const pathGen = $derived(d3.geoPath().projection(projection));

	const colorScale = d3
		.scaleSequential()
		.domain([0, 100])
		.interpolator(d3.interpolateReds);

	const legendWidth = 260;
	const legendHeight = 10;
	const legendX = 40;
	const legendY = 20;

	const legendScale = d3.scaleLinear().domain([0, 100]).range([0, legendWidth]);
	const legendTicks = legendScale.ticks(5);
</script>

<h1>Class 9 - D3 US State Map</h1>

<div>
	{#if hovered}
		<strong>{hovered.name}</strong>: {hovered.value}
	{:else}
		<em>Hover a state</em>
	{/if}
</div>

<svg {width} {height}>
	<g transform="translate(0, 30)">
		{#each usStates as state}
			{@const name = state.properties.name}
			{@const value = values[name]}
			<path
				d={pathGen(state)}
				fill={colorScale(values[state.properties.name])}
				stroke="#fff"
				stroke-width="0.5"
				class:hovered={hovered?.name === name}
				onmouseenter={() => (hovered = { name, value })}
				onmouseleave={() => (hovered = null)}
				role="img"
			/>
		{/each}
	</g>

	<g transform={`translate(${legendX}, ${legendY})`}>
		{#each legendTicks as tick, i}
			{#if i < legendTicks.length - 1}
				<rect
					x={legendScale(tick)}
					width={legendScale(legendTicks[i + 1]) - legendScale(legendTicks[i])}
					height={legendHeight}
					fill={colorScale(tick)}
				/>
			{/if}
			<text x={legendScale(tick)} y={legendHeight + 15} text-anchor="middle">
				{tick}
			</text>
		{/each}
	</g>
</svg>

<style>
	.hovered {
		stroke: #000;
		stroke-width: 1;
	}
</style>
