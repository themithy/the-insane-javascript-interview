# Insane JavaScript interview

This is a compilation of hardest JavaScript questions that I could think
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
const Func = () => {}

const val = new Func() // ?
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

21. What is the difference between `Object.keys` and
    `Object.getOwnPropertyNames` ?

22. What would be the result of running the following code ?

```js
function a() {}
const b = () => {}

a.__proto__ == b.__proto__ // ?
```

23. What would be the result of running the following code ?

```js
Promise.resolve()
  .then(() => Promise.resolve('abc'))
  .then((val) => {
    console.log(typeof val) // ?
  })
```

24. What would be the result of the following expression ?

```js
1 + - 0 || new 0
```

25. What would be the result of running the following code ?

```js
async function print(val) {
  console.log(val)
}

async function run() {
  [1, 2, 3].forEach(async (val) => {
    await print('A' + val)
    print('B' + val)
  })
}

run()
```

26. What would be the result of the following expression ?

```js
Symbol('1') + '2'
```

27. What would be the result of running the following code ?

```js
var a = b, b = 1

console.log('a: ' + a) // ?
console.log('b: ' + b) // ?
```

28. What would be the result of running the following code ?

```js
var prop = 1

delete prop // ?
```

29. What would be the result of the following expression ?

```js
null == false
```

30. What would be the result of running the following code ?

```js
const a = new Date()
const b = Object.assign(Object.create(Date.prototype), a)

a.toString() == b.toString() // ?
```

31. What would be the result of running the following code ?

```js
const obj = {}
const arr = []

obj[5] = true
arr[5] = true

obj['5'] // ?
arr['5'] // ?
```

32. What would be the result of running the following code ?

```js
const arr = [ 1, 2, 3 ]
arr[7.5] = true

arr.length = 0

Object.getOwnPropertyNames(arr) // ?
```

33. What would be the result of running the following code ?

```js
function func() {
  this.prop = 2
  return { prop: 4 }
}

func.prototype.prop = 6

const obj = new func()

obj.prop // ?
```

34. What would be the result of running the following code ?

```js
const arr = []
Object.setPrototypeOf(arr, Object.prototype)

arr instanceof Array // ?
```

35. What would be the result of running the following code ?

```js
[ 0, 2, 4 ].flatMap((x) => [ x, x + 1 ])
```

36. What would be the result of the following expression ?

```js
new Int8Array instanceof Array
```

37. What would be the result of running the following code ?

```js
let i = 0

for (; i < 10; i++) {
  setTimeout(() => console.log('i=' + i))
}
```

38. What would be the result of running the following code ?

```js
const obj = { a: true }

for (const v of obj) {
  console.log(v)
}
```

39. What would be the result of running the following code ?

```js
const arr = []
const it = arr[Symbol.iterator]()

it.next() // ?
```

40.  What would be the result of running the following code ?

```js
const map = new Map()

map['a'] = true
map.set('b', true)

map.get('a') // ?
map['b'] // ?
```

41.  What would be the result of running the following code ?

```js
const arr = [1, 2, 3]

for (const a of arr) {
  if (a <= 3) {
    arr.push(a + 3)
  }
  console.log(a)
}
```

42.  What would be the result of running the following code ?

```js
const obj = {}

Object.defineProperty(obj, 'a', {})
Object.defineProperty(obj, 'b', { get: () => {} })
Object.defineProperty(obj, 'c', { set: () => {} })

Object.getOwnPropertyNames(obj) // ?
```

43. What would be the result of the following expression ?

```js
NaN ** 0
```

44. What would be the result of the following expression ?

```js
JSON.parse('{}').__proto__
```

45. What would be the result of running the following code ?

```js
function Func() {
  return new this.constructor()
}

new Func()
```

46. What would be the result of the following expressions ?

```js
// 1.
true + 1 // ?

// 2.
true + '1' // ?
```

Final one. Why are all those questions so difficult ?
