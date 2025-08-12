<script>
    const xArray = [50, 100, 150, 200, 250];

    const flag = 180;

    const dataArray = [
        {
            cx: 50,
            cy: 150,
            r: 25
        },
        {
            cx: 100,
            cy: 150,
            r: 20
        },
        {
            cx: 150,
            cy: 150,
            r: 15
        },
        {
            cx: 200,
            cy: 150,
            r: 10
        },
        {
            cx: 250,
            cy: 150,
            r: 5
        }
    ]
</script>

<svg width="800" height="600" viewBox="0 0 800 600">
    <circle cx="50" cy="50" r="25" fill="red" />

    {#each xArray as cx}
        {#if cx > flag}
            <circle 
                {cx}
                cy="100"
                r="25"
                fill="red"
            />
        {:else}
            <circle 
                {cx}
                cy="100"
                r="25"
                fill="green"
            />
        {/if}
    {/each}

    {#each dataArray as {cx, cy, r}}
        <circle 
            {cx}
            {cy}
            {r}
            fill={cx > flag ? "red" : "green"}
        />
    {/each}
</svg>