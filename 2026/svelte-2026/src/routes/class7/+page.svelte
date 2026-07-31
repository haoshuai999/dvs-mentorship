<script>
	import * as d3 from "d3";

	let data = [
		{ date: new Date("2026-07-01"), sales: 18 },
		{ date: new Date("2026-08-01"), sales: 24 },
		{ date: new Date("2026-09-01"), sales: 21 },
		{ date: new Date("2026-10-01"), sales: 29 },
		{ date: new Date("2026-11-01"), sales: 35 },
		{ date: new Date("2026-12-01"), sales: 31 },
		{ date: new Date("2027-01-01"), sales: 40 },
	];

	const margin = { top: 30, right: 50, bottom: 50, left: 50 };
	const width = 600 - margin.left - margin.right;
	const height = 400 - margin.top - margin.bottom;

	const xScale = $derived(
		d3.scaleTime()
			.domain(d3.extent(data, (d) => d.date))
			.range([0, width])
            .nice()
	);

	const yScale = $derived(
		d3.scaleLinear()
			.domain([0, d3.max(data, (d) => d.sales)])
			.nice()
			.range([height, 0])
	);

	const linePath = $derived(
		d3.line()
			.x((d) => xScale(d.date))
			.y((d) => yScale(d.sales))
			.curve(d3.curveMonotoneX)(data)
	);

	const xTicks = $derived(xScale.ticks(5));
	const yTicks = $derived(yScale.ticks(5));
	const formatDate = d3.timeFormat("%b. %Y");
</script>

<h1>Class 7 - D3 Line Chart</h1>

<svg
	width={width + margin.left + margin.right}
	height={height + margin.top + margin.bottom}
>
	<g transform="translate({margin.left},{margin.top})">

        <!-- Y axis and optional gridlines -->
		<g>
			<line y2={height} stroke="black" />
			{#each yTicks as tick}
                <!-- Grid lines -->
                <line
                    x1="0"
                    x2={width}
                    y1={yScale(tick)}
                    y2={yScale(tick)}
                    stroke="lightgray"
                    stroke-dasharray="4 4"
                />
				<line x1="-6" y1={yScale(tick)} y2={yScale(tick)} stroke="black" />
				<text
					x="-10"
					y={yScale(tick)}
					dy="0.32em"
					text-anchor="end"
					font-family="sans-serif"
					font-size="10px"
					fill="black"
				>
					{tick}
				</text>
			{/each}
		</g>

        <!-- X axis and optional gridlines -->
		<g transform="translate(0,{height})">
			<line x2={width} stroke="black" />
			{#each xTicks as tick}
            	<line
					x1={xScale(tick)}
					x2={xScale(tick)}
					y1="0"
                    y2={-height}
					stroke="lightgray"
                    stroke-dasharray="4 4"
				/>
				<line
					x1={xScale(tick)}
					x2={xScale(tick)}
					y2="6"
					stroke="black"
				/>
				<text
					x={xScale(tick)}
					y="22"
					text-anchor="middle"
					font-family="sans-serif"
					font-size="10px"
					fill="black"
				>
					{formatDate(tick)}
				</text>
			{/each}
		</g>

		<!-- Line path -->
		<path d={linePath} fill="none" stroke="green" stroke-width="3" />

		<!-- Data points -->
		{#each data as d}
			<circle cx={xScale(d.date)} cy={yScale(d.sales)} r="5" fill="orange" />
		{/each}

		<!-- Point labels -->
		{#each data as d}
			<text
				x={xScale(d.date)}
				y={yScale(d.sales) - 10}
				text-anchor="middle"
				font-size="12px"
				fill="#1f2937"
			>
				{d.sales}
			</text>
		{/each}
	</g>
</svg>

<style>
	h1 {
		font-family: sans-serif;
		margin-bottom: 10px;
	}
</style>
