# Insane JavaScript interview

This is a compilation of twenty hardest JavaScript questions that I could think
of. The difficulty level is beyond the level of sanity and even skilled
programmers should never be bothered with such language features.  But if you
are interested in the less-known parts of the spec - give it a try!

1. What would be the result of the following expression ?

```js
console.log(!'0' == '0') // ?
```

2. What would be the result of running the following code ?

```js
undefined = null // ?
```

3. What would be the result of running the following code ?

```js
var a = 1

{
  let a = a + 1
  console.log(a) // ?
}
```

4. How to reliably check whether a variable is an object ?

5. What would be the result of running the following code ?

```js
function func() {
  console.log(Array.isArray(arguments)) // ?
}

func(1, 2)
```

6. How to detect whether a function has been called as a constructor ?

7. What would be the result of running the following two lines of code ?

```js
var a = new One() // ?
var b = new Two() // ?

function One() {} 

class Two {}
```

8. What would be the result of running the following code ?

```js
var a = {}
var b = {}

Object.setPrototypeOf(a, b)
Object.setPrototypeOf(b, a)
```

9. What would be the result of the following expression ?

```js
typeof [] instanceof Array
```

10. What would be the result of the following expression ?

```js
1000 == 01750n
```

11. What would be the result of the following expression ?

```js
console.log(2**53 == 2**53 + 1) // ?
```

12. What would be the result of running the following code ?

```js
const obj = {}

obj[undefined] = 1
obj[false] = 2
obj[+0] = 3
obj[-0] = 4
obj['0'] = 5
obj[{}] = 6

console.log(Object.values(obj)) // ?
```

13. What would be the result of running the following code ?

```js
const Func = () => {
  this.prop = 5
}

let val = new Func() // ?
```

14. What would be the result of running the following code ?

```js
function Tree() {}

const tree = new Tree()

console.log(tree.constructor.prototype.__proto__.__proto__) // ?
```

15. What would be the result of running the following code ?

```js
const target = new Number(5)
const proxy = new Proxy(target, {})

console.log(proxy.toString()) // ?
```

16. What is the difference between `Object.preventExtensions`, `Object.seal`
    and `Object.freeze` ?

17. What would be the result of running the following code ?

```js
function a() {
  const x = arguments[0]

  const b = () => {
    const y = arguments[0]
    console.log(y) // ?
  }

  b(2 * x)
}

a(10)
```

18. What would be the result of running the following code ?

```js
const key = Symbol('abc')

const obj = { [key]: 'def' }

console.log(JSON.stringify(obj)) // ?
```

19. What would be the result of the following expression ?

```js
false.__proto__
```

20. What would be the result of the following expression ?

```js
typeof /abc/
```

