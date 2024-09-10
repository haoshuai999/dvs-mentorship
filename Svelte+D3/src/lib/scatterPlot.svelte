<script>
	import * as d3 from "d3"
	
	// Hard code the data
	const data = [
		{
			date: "2023-02-01",
			value: 45
		},
		{
			date: "2023-05-01",
			value: 25
		},
		{
			date: "2023-08-01",
			value: 80
		},
	]

	// Declare the chart dimensions and margins.
	const width = 640;
	const height = 400;
	const marginTop = 20;
	const marginRight = 20;
	const marginBottom = 30;
	const marginLeft = 40;

	// Declare the x (horizontal position) scale.
	const x = d3.scaleUtc()
		.domain([new Date("2023-01-01"), new Date("2024-01-01")])
		.range([marginLeft, width - marginRight]);

	// Declare the y (vertical position) scale.
	const y = d3.scaleLinear()
		.domain([0, 100])
		.range([height - marginBottom, marginTop]);

	// Declare the format of the date
	const formatDate = d3.utcFormat("%b %Y");

	// onMount(() => {
	// 	// Create the SVG container.
	// 	const svg = d3.select("#container")
	// 		.append("svg")
	// 		.attr("width", width)
	// 		.attr("height", height);

	// 	svg.append("g")
	// 		.attr("transform", `translate(0,${height - marginBottom})`)
	// 		.call(d3.axisBottom(x));

	// 	// Add the y-axis.
	// 	svg.append("g")
	// 		.attr("transform", `translate(${marginLeft},0)`)
	// 		.call(d3.axisLeft(y))
	// 		.call(g => g.select(".domain").remove());

	// 	svg.append("g")
	// 		// .selectAll("circle")
	// 		// .data(data)
	// 		// .enter()
	// 		// .append("circle")
	// 		.selectAll()
	// 		.data(data)
	// 		.join("circle")
	// 		.attr("cx", d => x(new Date(d.date)))
	// 		.attr("cy", d => y(d.value))
	// 		.attr("r", 10)
	// 		.attr("fill", "red");
	// })
</script>

<!-- <svelte:window bind:innerWidth={width} bind:innerHeight={height} /> -->
<svg
	width={width}
	height={height}
	xmlns="http://www.w3.org/2000/svg"
>
	<g 
		class="x-axis"
		transform={`translate(0, ${height - marginBottom})`}
	>
		{#each x.ticks() as tick}
			<g
				transform={`translate(${x(tick)},0)`}
			>
				<line 
					y2={6}
					stroke="black"
				/>
				<text
					y={9}
					dy="0.71em"
					fill="black"
					font-size={10}
					text-anchor="middle"
				>
					{formatDate(tick)}
				</text>
			</g>
		{/each}
	</g>
	<g
		class="y-axis"
		transform={`translate(${marginLeft},0)`}
	>
		{#each y.ticks() as tick}
			<g
				transform={`translate(0,${y(tick)})`}
			>
				<line 
					x2={-6}
					stroke="black"
				/>
				<text
					x={-9}
					dy="0.32em"
					fill="black"
					font-size={10}
					text-anchor="end"
				>
					{tick}
				</text>
			</g>
		{/each}
	</g>
	<g
		class="dots"
	>
		{#each data as d}
			<circle
				cx={x(new Date(d.date))}
				cy={y(d.value)}
				r={10}
				fill="red"
			/>
		{/each}
	</g>
</svg>
<!-- <div id="container"></div> -->

<style>
	
</style>