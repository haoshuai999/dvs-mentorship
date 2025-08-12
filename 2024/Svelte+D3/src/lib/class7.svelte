<script>
    // Object section
    let e = {
        make: "nissan",
        model: "rogue",
        year: 2020
    }

    e.cylinder = 6;

    delete e.year

    console.log(`make: ${e.make}`)

    Object.keys(e).forEach(key => console.log(key))

    Object.values(e).forEach(value => console.log(value))

    Object.entries(e).forEach(([key, value]) => {
        console.log(`${key}: ${value}`)
    })

    for (let [key, value] of Object.entries(e)) {
        console.log(`${key}: ${value}`)
    }

    // console.log(e);

    // Function section
    function testFunc(flag){
        while (flag < 5) {
            console.log(flag)
            flag++
        }

        return flag
    }

    let result = testFunc(1)
    // console.log(result)

    const testFunc2 = (flag) => {
        for(let i = 0; i < flag; i++) {
            if(i < 10) {
                // console.log(i)
            }
        }
    }

    testFunc2(14)
</script>