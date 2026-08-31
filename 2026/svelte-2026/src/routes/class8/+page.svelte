<script>
	import * as d3 from "d3";

	let data = [
		{ label: "Apples", value: 42 },
		{ label: "Bananas", value: 78 },
		{ label: "Cherries", value: 55 },
	];

	const width = 420;
	const height = 420;
	const radius = 200;
	const labelRadius = 120;

	const colorScale = d3
		.scaleOrdinal()
		.domain(data.map((d) => d.label))
		.range(d3.schemeCategory10);

	const pie = d3
		.pie()
		.value((d) => d.value)
		.sort(null)(data);

	const arcPath = d3
		.arc()
		.innerRadius(radius * 0.3)
		.outerRadius(radius);

	const labelArcPath = d3
		.arc()
		.innerRadius(labelRadius)
		.outerRadius(labelRadius);
</script>

<h1>Class 8: D3 Pie Chart</h1>

<svg {width} {height}>
	<g transform={`translate(${width / 2}, ${height / 2})`}>
		{#each pie as slice}
			<path d={arcPath(slice)} fill={colorScale(slice.data.label)} />
			<text
				transform={`translate(${labelArcPath.centroid(slice)})`}
				text-anchor="middle"
				alignment-baseline="middle"
				font-size="14px"
				fill="#fff"
			>
				<tspan>{slice.data.label}</tspan>
				<tspan x="0" dy="1.2em">
					{slice.data.value}
				</tspan>
			</text>
		{/each}
	</g>
</svg>
