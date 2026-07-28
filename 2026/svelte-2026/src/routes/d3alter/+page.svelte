<script>
    import * as d3 from "d3";

    // -------------------------------------------------------------------------
    // WHY $effect IS NOT IDEAL FOR D3 CHARTS
    //
    // 1. Re-runs the ENTIRE chart setup on every reactive dependency change.
    //    If `data` or `width` changes, D3 tears down and redraws everything —
    //    no smooth transitions, no fine-grained updates.
    //
    // 2. Mixing DOM concerns: $effect runs after the DOM is painted, so the
    //    SVG element must already exist via a bind:this. This tightly couples
    //    the imperative D3 code to Svelte's reactive lifecycle in a fragile way.
    //
    // 3. Hard to split responsibilities: with $effect, scales, axes, and bars
    //    all live in one monolithic block. In the ideal approach, you'd use
    //    $derived to compute scales/domains reactively and declarative {#each}
    //    blocks to render bars — keeping each concern separate and readable.
    //
    // 4. Cleanup is manual and error-prone: you must remember to remove old
    //    SVG children in the effect body, otherwise elements accumulate on
    //    every re-run.
    // -------------------------------------------------------------------------

    const data = [
        { label: "Apples", value: 42 },
        { label: "Bananas", value: 78 },
        { label: "Cherries", value: 55 },
        { label: "Oranges", value: 30 },
        { label: "Strawberries", value: 91 },
        { label: "Kiwi", value: 63 },
    ];

    const margin = { top: 30, right: 20, bottom: 40, left: 50 };
    const width = 600 - margin.left - margin.right;
    const height = 400 - margin.top - margin.bottom;

    // bind:this gives us a reference to the SVG element once it's in the DOM
    let svgEl = $state(null);

    $effect(() => {
        // Guard: do nothing until the SVG element is mounted
        if (!svgEl) return;

        // ---- Teardown: remove everything from the previous run ----
        // This is necessary because $effect re-runs whenever its reactive
        // dependencies change. Without this, bars and axes would stack up.
        d3.select(svgEl).selectAll("*").remove();

        // ---- Build the chart from scratch every time ----
        const svg = d3
            .select(svgEl)
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            .attr("transform", `translate(${margin.left},${margin.top})`);

        // X scale — band scale for categorical labels
        const xScale = d3
            .scaleBand()
            .domain(data.map((d) => d.label))
            .range([0, width])
            .padding(0.3);

        // Y scale — linear scale for values
        const yScale = d3
            .scaleLinear()
            .domain([0, d3.max(data, (d) => d.value)])
            .nice()
            .range([height, 0]);

        // X axis
        svg.append("g")
            .attr("transform", `translate(0,${height})`)
            .call(d3.axisBottom(xScale));

        // Y axis
        svg.append("g").call(d3.axisLeft(yScale).ticks(6));

        // Bars
        svg.selectAll(".bar")
            .data(data)
            .join("rect")
            .attr("class", "bar")
            .attr("x", (d) => xScale(d.label))
            .attr("y", (d) => yScale(d.value))
            .attr("width", xScale.bandwidth())
            .attr("height", (d) => height - yScale(d.value))
            .attr("fill", "steelblue")
            .attr("rx", 3);

        // Value labels above each bar
        svg.selectAll(".label")
            .data(data)
            .join("text")
            .attr("class", "label")
            .attr("x", (d) => xScale(d.label) + xScale.bandwidth() / 2)
            .attr("y", (d) => yScale(d.value) - 6)
            .attr("text-anchor", "middle")
            .attr("font-size", "12px")
            .attr("fill", "#333")
            .text((d) => d.value);
    });
</script>

<h1>Class 6 — D3 Bar Chart (via <code>$effect</code>)</h1>

<p>
    This chart is drawn entirely inside a <code>$effect</code> block. See the
    code comments for why a reactive <code>$derived</code> + declarative approach
    is usually preferred over this pattern.
</p>

<!-- bind:this wires the DOM element into our reactive variable -->
<svg bind:this={svgEl}></svg>

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
