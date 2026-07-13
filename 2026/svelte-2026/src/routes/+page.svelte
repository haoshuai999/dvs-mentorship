<script>
    let graph = "svg";
    let src = "./favicon.svg";

    let fruits = ["apple", "banana", "orange", "peach"];

    let count = $state(0);

    function increase() {
        count++;
    }

    let double = $derived(count * 2);
</script>

<h1>Hello World</h1>
<p>Let's create some {graph.toUpperCase()}s!</p>
<img {src} alt="Svelte {graph} logo" />

{#each fruits as fruit}
    <div class="background" style:background="green">
        {#if fruit !== "peach"}
            <p class="item">{fruit}</p>
        {/if}
    </div>
{/each}

<button onclick={increase}>Clicked {count} times</button>

<p>Double value: {double}</p>

<style>
    .background {
        padding: 8px;
        background-color: blue;
    }

    .item {
        color: white;
    }
</style>
