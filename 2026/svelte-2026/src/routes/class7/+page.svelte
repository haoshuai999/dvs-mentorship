<script>
    import * as d3 from "d3";

    let data = [
        { date: "2026-07-01", sales: "18" },
        { date: "2026-08-01", sales: "24" },
        { date: "2026-09-01", sales: "21" },
        { date: "2026-10-01", sales: "29" },
        { date: "2026-11-01", sales: "35" },
        { date: "2026-12-01", sales: "31" },
        { date: "2027-01-01", sales: "40" },
    ];

    const margin = { top: 30, right: 50, bottom: 50, left: 50 };
    const svgWidth = 600;
    const svgHeight = 400;
    const width = svgWidth - margin.left - margin.right;
    const height = svgHeight - margin.top - margin.bottom;

    let xScale = $derived(
        d3
            .scaleTime()
            .domain(d3.extent(data, (d) => new Date(d.date)))
            .range([0, width])
            .nice(),
    );

    let yScale = $derived(
        d3
            .scaleLinear()
            .domain([0, d3.max(data, (d) => +d.sales)])
            .range([height, 0])
            .nice(),
    );

    let linePath = $derived(
        d3
            .line()
            .x((d) => xScale(new Date(d.date)))
            .y((d) => yScale(+d.sales)),
    );

    let xTicks = $derived(xScale.ticks(5));
    let yTicks = $derived(yScale.ticks(5));

    let formatDate = d3.timeFormat("%b %Y");
</script>

<h1>Class 7: D3 Line Chart</h1>

<svg width={svgWidth} height={svgHeight}>
    <g transform={`translate(${margin.left},${margin.top})`}>
        <g>
            <line y2={height} stroke="#000" />
            {#each yTicks as tick}
                <line
                    x2={width}
                    y1={yScale(tick)}
                    y2={yScale(tick)}
                    stroke="lightgray"
                    stroke-dasharray="4 4"
                />
                <line
                    x1={-6}
                    x2={0}
                    y1={yScale(tick)}
                    y2={yScale(tick)}
                    stroke="#000"
                />
                <text x={-10} y={yScale(tick)} text-anchor="end" dy="0.32em">
                    {tick}
                </text>
            {/each}
        </g>
        <g transform={`translate(0,${height})`}>
            <line x2={width} stroke="#000" />
            {#each xTicks as tick}
                {#if xScale(tick) > 0}
                    <line
                        x1={xScale(tick)}
                        x2={xScale(tick)}
                        y2={-height}
                        stroke="lightgray"
                        stroke-dasharray="4 4"
                    />
                {/if}
                <line
                    x1={xScale(tick)}
                    x2={xScale(tick)}
                    y2={6}
                    stroke="#000"
                />
                <text x={xScale(tick)} y={20} text-anchor="middle">
                    {formatDate(tick)}
                </text>
            {/each}
        </g>
        <path d={linePath(data)} fill="none" stroke="green" stroke-width="3" />
        <g>
            {#each data as d}
                <circle
                    cx={xScale(new Date(d.date))}
                    cy={yScale(+d.sales)}
                    r={4}
                    fill="red"
                />
                <text
                    x={xScale(new Date(d.date))}
                    y={yScale(+d.sales) - 10}
                    text-anchor="middle"
                    font-size="16px"
                >
                    {d.sales}
                </text>
            {/each}
        </g>
    </g>
</svg>

<style>
  h1 {
    font-family: sans-serif;
    margin-bottom: 10px;
  }
</style>
