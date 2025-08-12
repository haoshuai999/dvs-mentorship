<script>
    const width = 600;
    const height = 600;

    const viewWidth = 400;
    const viewHeight = 400;

    const test = 100;

    const dimensions = [
        [0, 0, "cyan"],
        [200, 0, "pink"],
        [0, 200, "orange"],
        [200, 200, "grey"]
    ];

    $: {
        if (test === 100) {
            console.log("test");
        };

       dimensions.forEach(d => {
            console.log(d[0], d[1]);
       });
    }
</script>

<svg width={width} height={height} fill="none" xmlns="http://www.w3.org/2000/svg" viewBox={`0 0 ${viewWidth} ${viewHeight}`}>
    {#each dimensions as d, i}
        {#if test === 100}
            <rect 
                x={d[0]}
                y={d[1]}
                width="200"
                height="200"
                fill={d[2]}
            />
        {/if}
    {/each}
</svg>