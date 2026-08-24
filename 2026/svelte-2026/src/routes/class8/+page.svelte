<script>
  import * as d3 from "d3";

  let data = [
    { label: "Apples", value: 42 },
    { label: "Bananas", value: 78 },
    { label: "Cherries", value: 55 },
  ];

  const width = 640;
  const height = 420;
  const radius = 150;
  const labelRadius = 80;

  const color = d3
    .scaleOrdinal()
    .domain(data.map((d) => d.label))
    .range(d3.schemeTableau10);

  const pieData = $derived(
    d3
      .pie()
      .value((d) => d.value)
      .sort(null)(data),
  );

  const arcPath = d3.arc().innerRadius(0).outerRadius(radius);
  const labelArc = d3.arc().innerRadius(labelRadius).outerRadius(labelRadius);
  const total = $derived(d3.sum(data, (d) => d.value));
  const format = d3.format(".1f");
</script>

<h1>Class 8 - D3 Pie Chart</h1>

<div class="chart-wrap">
  <div class="legend">
    {#each data as item}
      <div class="legend-item">
        <span class="swatch" style:background={color(item.label)}></span>
        <span>{item.label}</span>
      </div>
    {/each}
  </div>
  <svg {width} {height} aria-label="Fruit distribution pie chart">
    <g transform="translate({width / 2},{height / 2})">
      {#each pieData as slice}
        <path
          d={arcPath(slice)}
          fill={color(slice.data.label)}
          stroke="#ffffff"
          stroke-width="2"
        />
      {/each}

      {#each pieData as slice}
        <text
          x={labelArc.centroid(slice)[0]}
          y={labelArc.centroid(slice)[1]}
          text-anchor="middle"
          dominant-baseline="middle"
          font-size="16px"
          fill="white"
        >
          {slice.data.label}
        </text>
        <text
          x={labelArc.centroid(slice)[0]}
          y={labelArc.centroid(slice)[1]}
          text-anchor="middle"
          dominant-baseline="middle"
          font-size="16px"
          fill="white"
        >
          {format((slice.data.value / total) * 100)}%
        </text>
      {/each}
    </g>
  </svg>
</div>

<style>
  .chart-wrap {
    display: flex;
    flex-direction: column;
  }

  .legend {
    display: flex;
    gap: 16px;
  }

  .legend-item {
    display: flex;
    gap: 4px;
    align-items: center;
    font-size: 14px;
  }

  .swatch {
    width: 14px;
    height: 14px;
    border-radius: 3px;
  }
</style>
