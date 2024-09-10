<script>
    // Array section
    let a = ["apple", "banana", "orange"];
    
    a.push("peach", "grape")

    a.unshift("strawberry", "watermelon")

    const newArray = ['firstItem', ...a, 'test'];

    a.shift()
    a.pop()
    a.splice(0, 1)

    console.log(a.indexOf("apple"))

    let b = a.map(d => d.toUpperCase())
    let c = a.filter(d => d.length > 5)
    let d = a.slice(0, 1)

    a.reverse()

    a.sort((a, b) => a.length - b.length)

    console.log(a)
    console.log(b)
    console.log(c)
    console.log(d)
</script>