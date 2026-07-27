<script>
    import * as d3 from "d3";

    let data = [
        { label: "Apples", value: 42 },
        { label: "Bananas", value: 78 },
        { label: "Cherries", value: 55 },
        { label: "Oranges", value: 30 },
        { label: "Strawberries", value: 91 },
        { label: "Kiwi", value: 63 },
    ];

    const margin = { top: 30, right: 20, bottom: 40, left: 50 };
    const width  = 600 - margin.left - margin.right;
    const height = 400 - margin.top  - margin.bottom;

    // $derived computes scales reactively whenever `data` changes.
    // No DOM access needed — these are pure value computations.
    const xScale = $derived(
        d3.scaleBand()
            .domain(data.map(d => d.label))
            .range([0, width])
            .padding(0.3)
    );

    const yScale = $derived(
        d3.scaleLinear()
            .domain([0, d3.max(data, d => d.value)])
            .nice()
            .range([height, 0])
    );

    // $derived tick arrays let the template render axes declaratively,
    // without ever calling d3.axisBottom/Left or touching the DOM.
    const xTicks = $derived(xScale.domain());
    const yTicks = $derived(yScale.ticks(6));

    // Alternatively, we can use bind:this + $effect to let D3 render the axes:
    // let xAxisEl = $state(null);
    // let yAxisEl = $state(null);
    // $effect(() => { if (xAxisEl) d3.select(xAxisEl).call(d3.axisBottom(xScale)); });
    // $effect(() => { if (yAxisEl) d3.select(yAxisEl).call(d3.axisLeft(yScale).ticks(6)); });
    // To provide full control, xTicks / yTicks can be generated as fully declarative SVG axis markup instead.
</script>

<h1>Class 6 — D3 Bar Chart (<code>$derived</code> + declarative SVG)</h1>

<p>
    Scales are computed with <code>$derived</code>. Bars, labels and axes are rendered
    with <code>{"{#each}"}</code> directly in the SVG markup.
</p>

<svg
    width={width + margin.left + margin.right}
    height={height + margin.top + margin.bottom}
>
    <g transform="translate({margin.left},{margin.top})">

        <!-- Bars — rendered declaratively, no D3 DOM manipulation needed -->
        {#each data as d}
            <rect
                x={xScale(d.label)}
                y={yScale(d.value)}
                width={xScale.bandwidth()}
                height={height - yScale(d.value)}
                fill="steelblue"
                rx="3"
            />
        {/each}

        <!-- Value labels above each bar -->
        {#each data as d}
            <text
                x={xScale(d.label) + xScale.bandwidth() / 2}
                y={yScale(d.value) - 6}
                text-anchor="middle"
                font-size="12px"
                fill="#333"
            >
                {d.value}
            </text>
        {/each}

        <!-- X axis — declarative SVG (previously: <g bind:this={xAxisEl} transform="translate(0,{height})"></g>) -->
        <g transform="translate(0,{height})">
            <!-- Baseline -->
            <line x2={width} stroke="#000" />
            {#each xTicks as tick}
                <!-- Tick mark -->
                <line
                    x1={xScale(tick) + xScale.bandwidth() / 2}
                    x2={xScale(tick) + xScale.bandwidth() / 2}
                    y2="6"
                    stroke="#000"
                />
                <!-- Tick label -->
                <text
                    x={xScale(tick) + xScale.bandwidth() / 2}
                    y="16"
                    text-anchor="middle"
                    font-family="sans-serif"
                    font-size="10px"
                    fill="#000"
                >
                    {tick}
                </text>
            {/each}
        </g>

        <!-- Y axis — declarative SVG (previously: <g bind:this={yAxisEl}></g>) -->
        <g>
            <!-- Baseline -->
            <line y2={height} stroke="#000" />
            {#each yTicks as tick}
                <!-- Tick mark -->
                <line
                    x1="-6"
                    y1={yScale(tick)}
                    y2={yScale(tick)}
                    stroke="#000"
                />
                <!-- Tick label -->
                <text
                    x="-9"
                    y={yScale(tick)}
                    dy="0.3em"
                    text-anchor="end"
                    font-family="sans-serif"
                    font-size="10px"
                    fill="#000"
                >
                    {tick}
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
    p {
        font-family: sans-serif;
        margin-bottom: 20px;
    }
</style>
