<script>
    import { onMount } from "svelte";

    let graph = "svg";
    let src = "./favicon.svg";

    let count = $state(0);
    let countArray = $state([]);
    let sum = $derived(countArray.reduce((total, number) => total + number, 0));
    let timePassed = $state(0);
    let interval = $state(1000);

    function increase() {
        count++;
        countArray.push(countArray.length + 1);
    }

    let dom = "<b><i>Hello World!</i></b>";

    onMount(() => {
        console.log(count, countArray);
    });

    $effect(() => {
        const id = setInterval(() => {
            if (timePassed <= 50) timePassed += 1;
        }, interval);

        return () => clearInterval(id);
    });

    $inspect(sum, interval);
</script>

<h1>Class 5</h1>
<p>{@html dom}</p>
<p>Time passed: {timePassed}</p>

<button onclick={increase}>Clicked {count} times</button>

<button onclick={() => (interval /= 2)}>speed up</button>

{#each countArray as item}
    <p>{item}</p>
{/each}

<p>Sum: {sum}</p>
