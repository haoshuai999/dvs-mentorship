<script>
    console.log("Hello World!");
    console.log(1 + 1);

    const a = 25;

    if (a > 10) {
        console.log("Hello World");
    }

    const b = [10, 20, 30];

    for(let i = 0; i < b.length; i++) {
        console.log(b[i]);
    }

    b.forEach( value => {
        console.log(value);
    })
</script>

<div>
    Hello World!
    <p>Test texts</p>
    <svg width="100" height="100" viewBox="25 0 100 100">
        <circle cx="50" cy="50" r="25" fill="red" />
    </svg>
</div>

<style>
    div {
        background-color: orange;
    }

    p {
        color: red;
    }

    circle {
        fill: blue;
    }
</style>