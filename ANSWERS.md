# Insane JavaScript interview (with answers)

1. What would be the result of the following expression ?

```js
console.log(!'0' == '0') // ?
```

ANSWER

The mentioned expression returns `true` which is contrary to what might have
been perceived as an easy application of Boolean algebra. This due to the fact
how the comparison operator works which is defined in the ECMAScript spec as
the *Abstract Equality Comparison Algorithm*.

Under the hood the left-hand side is first converted to boolean using the
`toBoolean` algorithm and then converted to a number for comparison. The
right-hand side is converted to number right away using the `toNumber`
algorithm.  Those two algorithms treat the string `'0'` differently, hence the
curiosity.  Any non-empty string will resolve to *true* when converting to
boolean. However when converting the string to number, the algorithm will try to
parse the string to obtain a numeric value, which means that `'0'` will be
converted to a zero value.

The following example gives an illustration of conversion steps:

```js
!'0' == '0'
!true == '0'
false == '0'
0 == 0
```

2. What would be the result of running the following code ?

```js
undefined = null // ?
```

ANSWER

Contrary to `null` which is a literal of primitive type *null*, the *undefined*
type has no literals. Neither `undefined` is a reserved word but can be used as
a variable name and assigned any value. In global scope `undefined` is a
property of the global object and its value is set to *undefined*. In recent
ECMAscript specification this property has been defined as non-configurable and
non-writable. However in older browsers it was possible to change its value
thus leading to an unexpected behaviour. Assigning the *right* value again is
possible via the `void` operator:

```js
undefined = void 0
```

3. What would be the result of running the following code ?

```js
var a = 1

{
  let a = a + 1
  console.log(a) // ?
}
```

ANSWER

The second assignment to `a` will throw with a `ReferenceError`.  The `let`
declarations are moved to the top of the block but unlike `var` declarations
are not initialized with *undefined*.  So in the expression `a + 1` the "a"
refers already to the block-scoped variable and not the global-scoped one.
However `let` variables are defined only after the evaluation of the whole
expression, hence the error.  It is an edge-case example of a concept that is
called "temporal dead zone", the following example is more clear but
will yield the same result.

```js
var a = 1

{
  console.log(a) // throws
  let a = 2
}
```

4. How to reliably check whether a variable is an object ?

ANSWER

There are eight types in JavaScript: seven so called "primitive types", and the
object type. The type of a variable can determined using the `typeof` operator,
however with some limitations. The return values of that operator are:
'undefined', 'boolean', 'number', 'bigint', 'string', 'symbol', 'function', and
'object'. A well-know quirk of JavaScript is that `typeof null === 'object'`,
so a special care of this case need to be taken. Furthermore a function is also
an object in JavaScript. So a reliable way to check for an object is:

```js
function isObject(val) {
  return val !== null && (typeof val === 'object' || typeof val === 'function')
}
```

Another, quick way is to check whether a conversion to object yields the same
reference:

```js
function isObject(val) {
  return val == Object(val)
}
```

It is important to note that the `instanceof` operator cannot be used in a
reliable way:

```js
Object.create(null) instanceof Object // false
```

5. What would be the result of running the following code ?

```js
function func() {
  console.log(Array.isArray(arguments)) // ?
}

func(1, 2)
  ```

ANSWER

The result is `false`. `arguments` is an array-like object, which means it has
the `length` property and integer-based properties, but is not a real array.
Let us investigate this object a bit more:

```js
console.log(Object.prototype.toString.call(arguments))  // '[object Arguments]'
console.log(arguments.forEach)                          // undefined
console.log(arguments.__proto__ == Object.prototype)    // true
```

6. How to detect whether a function has been called as a constructor ?

ANSWER

The `new.target` pseudo-property would be set if the function was called with
the `new` operator. Otherwise it will be undefined.

7. What would be the result of running the following two lines of code ?

```js
var a = new One() // ?
var b = new Two() // ?

function One() {} 

class Two {}
```

ANSWER

The question is concerned with a concept called *hoisting*. This functionality
moves a declaration to the top of the scope (file or function). Functions are
hoisted but classes are not, so the second line will throw with
`ReferenceError`. This is also a good example that classes in JS are not only
what is called a "syntactic sugar", but semantic differences do exist.

8. What would be the result of running the following code ?

```js
var a = {}
var b = {}

Object.setPrototypeOf(a, b)
Object.setPrototypeOf(b, a)
```

It is not possible to introduce circular dependencies in the prototype chain so
the last line will throw with a `TypeError`.

9. What would be the result of the following expression ?

```js
typeof [] instanceof Array
```

ANSWER

The answer is `false`. The key to this question is to remember in which order
the operators are evaluated (`typeof` and `instanceof` are both operators, not
functions). The `typeof` operator will evaluate first and return the string
'object'.  A string is a primitive type and therefore not an instance of any
object, hence the whole expression evaluates to `false`. Note that it might be
tempting to answer *'boolean'*, however that would require that the operators
be evaluated from right to left, which is not the case.

10. What would be the result of the following expression ?

```js
1000 == 01750n
```

ANSWER

This expression will throw, because a legacy octal number notation (right-hand
side) cannot be used for big int literals (the "n" suffix means that the value
should be of `BigInt` primitive type). `01750` is of course an octal
representation of `1000` in decimal system. So the following holds:

```js
1000 == 0o1750n   // true - note that 'o' was missing previously
1000 === 0o1750n  // false - because of type mismatch
```

11. What would be the result of the following expression ?

```js
console.log(2**53 == 2**53 + 1) // ?
```

ANSWER

The result would be `true`.  JavaScript has only one type for numbers which is
a double float (or had until very recently). In case of very big numbers the
float representation looses precision and cannot be used to adequately
represent integer numbers. The maximum "safe" integer can be accessed via
`Number.MAX_SAFE_INTEGER`.

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

ANSWER

The answer is `[5, 1, 2, 6]`. Object property keys are converted to string
using the `toString` algorithm. *undefined* is converted to 'undefined' and
*false* to 'false'. Both minus and plus zeros are converted to '0'. An object
is converted to '[object Object]' using the `Object.prototype.toString`
function.

The order of the properties is somewhat ambiguous but not random. Integer
properties go first, then other properties in insertion order.

13. What would be the result of running the following code ?

```js
const Func = () => {}

const val = new Func() // ?
```

ANSWER

This example will throw with a `TypeError` as arrow functions cannot be called
as constructors. Also for this reason the following expression will yield
`undefined`.

```js
const Func = () => {}

Func.prototype // undefined
```

Furthermore arrow functions have no binding to `this` and `arguments` keywords.

14. What would be the result of running the following code ?

```js
function Tree() {}

const tree = new Tree()

console.log(tree.constructor.prototype.__proto__.__proto__) // ?
```

ANSWER

The result is `null`, which is the last element of prototype chain. The
property `constructor` returns a reference to the function that created the
object. Consult the following for clarity:

```js
tree.constructor  // Tree
tree.constructor.prototype  // Tree.prototype
tree.constructor.prototype.__proto__  // Object.prototype
tree.constructor.prototype.__proto__.__proto__  // null
```

15. What would be the result of running the following code ?

```js
const target = new Number(5)
const proxy = new Proxy(target, {})

console.log(proxy.toString()) // ?
```

ANSWER

The code will throw a `TypeError`. The proxy mechanism can intercept most of
the operations and there is no easy way to tell the proxy apart from its
target. However not all operations are intercepted by proxies. The methods
defined on some built-in prototypes (like `Number.prototype` in this example)
are not generic and access the object's value by an "internal slot" - which is
not intercepted, hence the error.

16. What is the difference between `Object.preventExtensions`, `Object.seal`
    and `Object.freeze` ?

An object property descriptor has six attributes, among them `configurable`,
and `writable`. `Object.preventExtensions` will prevent adding new properties.
`Object.seal` will additionally make all existing properties non-configurable.
`Object.freeze` will also make the properties `non-writable`.

Note that in order to make a data structure fully immutable one would have to
traverse the object's properties in a recursive manner. Check the following:

```js
const a = {
  b: {
    c: 1,
  }
}

Object.freeze(a)

a.b.c = 2  // works
a.b = 3    // does not work or throws
a = 4      // throws because of const
```

17. What would be the result of running the following code ?

```js
function first() {
  const x = arguments[0]

  const second = () => {
    const y = arguments[0]
    console.log(y) // ?
  }

  second(2 * x)
}

first(10)
```

ANSWER

The result will be `10`. Arrow functions do not bind their own `arguments`
keyword, so it will be taken from the outer scope, which is the same as for the
function "first". This example is similar to the previous one.

18. What would be the result of running the following code ?

```js
const key = Symbol('abc')

const obj = { [key]: 'def' }

console.log(JSON.stringify(obj)) // ?
```

ANSWER

The result is `{}`. Symbol-keyed properties are ignored when converting to
JSON.

19. What would be the result of the following expression ?

```js
false.__proto__
```

ANSWER

The result is `Boolean.prototype`. The value is converted silently to object
using the `toObject` algorithm and then property access is performed.
Conversion of *null* and *undefined* throws. Accessing a property of a
primitive type literal may look strange, but in fact it is a common practice in
day-to-day programming. Consult this:

```js
const str = 'abcdef'
str.includes('bcd')
```

20. What would be the result of the following expression ?

```js
typeof /abc/
```

ANSWER

The `/abc/` notation is a literal creating the `RegExp` object, so the result
is `object`.

21. What is the difference between `Object.keys` and
    `Object.getOwnPropertyNames` ?

ANSWER

`Object.keys` returns only enumerable properties but
`Object.getOwnPropertyNames` returns both enumerable and non-enumerable
properties. Consult this:

```js
const obj = {}

obj['a'] = 'a'
Object.defineProperty(obj, 'b', {})

console.log(Object.keys(obj))                 // ['a']
console.log(Object.getOwnPropertyNames(obj))  // ['a', 'b']
```

Properties initialized using syntax shorthands (e.g. simple assignment) are
always enumerable, but properties defined via `Object.defineProperty` are
non-enumerable by default.

