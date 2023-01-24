# Insane JavaScript interview (with answers)

QUESTION 1

What would be the result of the following expression ?

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

QUESTION 2

What would be the result of running the following code ?

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

QUESTION 3

What would be the result of running the following code ?

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
QUESTION 4

How to reliably check whether a variable is an object ?

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

QUESTION 5

What would be the result of running the following code ?

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

QUESTION 6

How to detect whether a function has been called as a constructor ?

ANSWER

The `new.target` pseudo-property would be set if the function was called with
the `new` operator. Otherwise it will be undefined.

QUESTION 7

What would be the result of running the following two lines of code ?

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

QUESTION 8

What would be the result of running the following code ?

```js
var a = {}
var b = {}

Object.setPrototypeOf(a, b)
Object.setPrototypeOf(b, a)
```

ANSWER

It is not possible to introduce circular dependencies in the prototype chain so
the last line will throw with a `TypeError`.

QUESTION 9

What would be the result of the following expression ?

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

QUESTION 10

What would be the result of the following expression ?

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

QUESTION 11

What would be the result of the following expression ?

```js
console.log(2**53 == 2**53 + 1) // ?
```

ANSWER

The result would be `true`.  JavaScript has only one type for numbers which is
a double float (or had until very recently). In case of very big numbers the
float representation looses precision and cannot be used to adequately
represent integer numbers. The maximum "safe" integer can be accessed via
`Number.MAX_SAFE_INTEGER`.

QUESTION 12

What would be the result of running the following code ?

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
using the `ToString` algorithm. *undefined* is converted to 'undefined' and
*false* to 'false'. Both minus and plus zeros are converted to '0'. An object
is converted to '[object Object]' using the `Object.prototype.toString`
function.

The order of the properties is somewhat ambiguous but not random. Integer
properties go first, then other properties in insertion order.

QUESTION 13

What would be the result of running the following code ?

```js
const Func = () => {}

const val = new Func() // ?
```

ANSWER

This example will throw with a `TypeError` as arrow functions cannot be called
as constructors. Also for this reason the following expression will yield
`undefined`:

```js
const Func = () => {}

Func.prototype // undefined
```

Furthermore arrow functions have no binding to `this` and `arguments` keywords.

QUESTION 14

What would be the result of running the following code ?

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

QUESTION 15

What would be the result of running the following code ?

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

QUESTION 16

What is the difference between `Object.preventExtensions`, `Object.seal` and
`Object.freeze` ?

ANSWER

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

QUESTION 17

What would be the result of running the following code ?

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

QUESTION 18

What would be the result of running the following code ?

```js
const key = Symbol('abc')

const obj = { [key]: 'def' }

console.log(JSON.stringify(obj)) // ?
```

ANSWER

The result is `{}`. Symbol-keyed properties are ignored when converting to
JSON.

QUESTION 19

What would be the result of the following expression ?

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

QUESTION 20

What would be the result of the following expression ?

```js
typeof /abc/
```

ANSWER

The `/abc/` notation is a literal creating the `RegExp` object, so the result
is `object`.

QUESTION 21

What is the difference between `Object.keys` and `Object.getOwnPropertyNames` ?

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

QUESTION 22

What would be the result of running the following code ?

```js
function a() {}
const b = () => {}

a.__proto__ == b.__proto__ // ?
```

ANSWER

The result would be `true`. Regular functions and arrow functions share the
same prototype which is `Function.prototype`.

Note: `b.__proto__` which equals `Object.getPrototypeOf(b)` should not be
mistaken with `b.prototype`.

QUESTION 23

What would be the result of running the following code ?

```js
Promise.resolve()
  .then(() => Promise.resolve('abc'))
  .then((val) => {
    console.log(typeof val) // ?
  })
```

ANSWER

The `then` function returns a promise that is fulfilled in most cases with a
value returned by the callback. However if the callback returns another promise
then this promise will be chained instead of wrapped, which is a special case.

So the answer here is `string`, although it might be tempting to answer
'object', because one might expect that the variable *val* is a promise, not a
string, which is wrong however.

QUESTION 24

What would be the result of the following expression ?

```js
1 + - 0 || new 0
```

ANSWER

The result would be `1`. The key to this question is to remember that
arithmetic operators take precedence over logical operators, so the addition
operator will be evaluated before the logical OR. Furthermore because of the
concept known as conditional evaluation (or "short-circuiting") the second
expression, which would otherwise throw because zero is not a constructor, will
not be evaluated. Lastly, the minus in front of the first zero is the unary
negation operator that converts positive zero to negative zero. In this
particular example it adds nothing but a confusion to the reader.

QUESTION 25

What would be the result of running the following code ?

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

ANSWER

The code will print the strings in the following order: `B1 B2 B3 A1 A2 A3`.
The function `Array.prototype.forEach` does not work with asynchronous
functions (or promises). If you are looking for a way to execute the promises
in sequential order use the `for ... of` loop.

QUESTION 26

What would be the result of the following expression ?

```js
Symbol('1') + '2'
```

ANSWER

The addition operator works the following way: if any of the operands is a
string, then both operands are converted to a string and concatenated.
Otherwise both operands are converted to a number and the corresponding
arithmetic operation is performed.

Here the expression will throw with a `TypeError`, which is a behaviour defined
for symbol to string conversion in the `ToString` algorithm from the ECMAScript
specification. Note that this is different from `Symbol.prototype.toString`
function.

QUESTION 27

What would be the result of running the following code ?

```js
var a = b, b = 1

console.log('a: ' + a) // ?
console.log('b: ' + b) // ?
```

ANSWER

The result would be as follows:

```js
a: undefined
b: 1
```

The assignment of *b* to *a* happens before the variable *a* is initialized.
Declarations are hoisted anyway.

QUESTION 28

What would be the result of running the following code ?

```js
var prop = 1

delete prop // ?
```

ANSWER

The expression will return `false`. Global properties cannot be deleted from
the global scope using the `delete` operator.

QUESTION 29

What would be the result of the following expression ?

```js
null == false
```

ANSWER

Contrary to what might be expected the values would not be coerced and the
result would be `false`. In this case it does not matter whether the operator is `==` or `===`. It is a dusty corner of the "Abstract Equality Comparison" algorithm, in general it goes like this:

1. If operands are of the same type perform strict comparison, objects are
   compared by reference.
2. *Undefined* and *null* equal to each other and to nothing else.
3. If any operand is an object, convert it to primitive type, and:
4. Convert operands to number and compare.

Consider the following:

```js
'0' == false // true, both converted to number.
```

QUESTION 30

What would be the result of running the following code ?

```js
const a = new Date()
const b = Object.assign(Object.create(Date.prototype), a)

a.toString() == b.toString() // ?
```

ANSWER

Date object is an exotic object containing an internal slot which implies that
it cannot be simply copied via the `Object.assign` method. The last line will
throw with a `TypeError` complaining that a wrong object has been passed to
*toString*. Check this to gain some insight:

```js
Object.getOwnPropertyNames(new Date()) // []
```

QUESTION 31

What would be the result of running the following code ?

```js
const obj = {}
const arr = []

obj[5] = true
arr[5] = true

obj['5'] // ?
arr['5'] // ?
```

ANSWER

In both cases the result would be `true`. Only strings and symbols are accepted
as property keys so a number would be converted to string even for making array
element access. In this particular case there are no special mechanics for
arrays but they are implemented pretty much the same as regular objects
(almost).

QUESTION 32

What would be the result of running the following code ?

```js
const arr = [ 1, 2, 3 ]
arr[7.5] = true

arr.length = 0

Object.getOwnPropertyNames(arr) // ?
```

ANSWER

The result would be `[ 'length', '7.5' ]`. Assigning directly to the length
property deletes all array items that have the array index less than the array
length. Here the *7.5* property and the *length* property have no effect on
each other as *7.5* is not an array index.

QUESTION 33

What would be the result of running the following code ?

```js
function func() {
  this.prop = 2
  return { prop: 4 }
}

func.prototype.prop = 6

const obj = new func()

obj.prop // ?
```

ANSWER

An object is created and bound to *this* context when invoking a function with
the *new* operator.  This object is then returned as the result of the whole
expression. However one exception applies - if constructor returns a value,
then this value is used instead of the newly created object. So here the correct
answer for the question is `4`. Sometimes constructors are written in a more
explicit way, and return *this*, which is however not necessary.

```js
function func() {
  this.prop = 2
  return this // not necessary
}
```

QUESTION 34

What would be the result of running the following code ?

```js
const arr = []
Object.setPrototypeOf(arr, Object.prototype)

arr instanceof Array // ?
```

The result would be `false`. The *instanceof* operator uses the prototype chain
to perform the test, or more exactly it checks whether the constructor's
prototype is present in the prototype chain of the target. Here if we alter the
target's prototype, the operator will yield different result.

QUESTION 35

What would be the result of running the following code ?

```js
[1, 2].flatMap((x) => [x, [x + 1]])
```

ANSWER

The result would be `[1, [2], 2, [3]]`. The *flatMap* function flattens only
one level deep.

Note: In contrary to the *Array.prototype.map* function the *flatMap* function
can be used to add or remove elements from the array.

QUESTION 36

What would be the result of the following expression ?

```js
new Int8Array instanceof Array
```

ANSWER

The result would be `false`. Typed arrays follow a different prototype chain
that does not include the *Array.prototype*. Of course the *new* operator has
precedence over the *instanceof* operator. Below is the exact chain depicted:

```js
Int8Array.prototype -> TypedArray.prototype -> Object.prototype -> null
```

QUESTION 37

What would be the result of running the following code ?

```js
let i = 0

for (; i < 10; i++) {
  setTimeout(() => console.log('i=' + i))
}
```

ANSWER

The result would be ten times `i=10`.  This is of course not the indented
behaviour but we would rather expected the loop to print increasing integer
values. The explanation for this fact is that the calls to *console.log* are
moved to the stack and are executed only after the loop code has finished. We
know that Javascript has a garbage collector and that the variable *i* would
not be destroyed until there are valid references. But all references point to
one and the same *i* variable, which bears the value `10`, because it is the
final value after the loop operation.

The fix is rather obvious and it is to move the *declaration* into 
the for loop like this:

```js
for (let i = 0; i < 10; i++) {
  setTimeout(() => console.log('i=' + i))
}
```

But one question remains, why would that work ? We know that the *let*
statement creates a block-scoped variable, but we would rather expected that
the variable be created for the whole loop execution, don't we ? Actually the
mechanics cannot be explained with the *let* statement alone but we should dive
into the actual specification of the for loop. The interesting part is located
in the section *13.7.4.9* of the 10-th EcmaScript specification.

The algorithm called *CreatePerIterationEnvironment* explains that a lexical
environment is created for every loop iteration, not only for the whole loop
execution. So in our case the *i* variable is created per every loop iteration.
This is of course only the case in which the variable is created with the *let*
statement.  In other cases the *perIterationBinding* set is empty, and thus the
per-iteration lexical environments are not created.

Note: Although Javascript does not support function overloading, it does
support statement overloading in some sense.

QUESTION 38

What would be the result of running the following code ?

```js
const obj = { a: true }

for (const v of obj) {
  console.log(v)
}
```

ANSWER

The code would throw with a `TypeError`. A regular object is not an iterable
object by default.

QUESTION 39

What would be the result of running the following code ?

```js
const arr = []
const it = arr[Symbol.iterator]()

it.next() // ?
```

ANSWER

The result would be `{ value: undefined, done: true }`, which is a return value
defined by the iteration protocol.

QUESTION 40

What would be the result of running the following code ?

```js
const map = new Map()

map['a'] = true
map.set('b', true)

map.get('a') // ?
map['b'] // ?
```

ANSWER

The result would be `undefined` in both cases. Map properties do not use
regular object semantics but are stored in an internal structure.  Consult the
following:

```js
Object.getOwnPropertyNames(map) // ['a']
```

QUESTION 41

What would be the result of running the following code ?

```js
const arr = [1, 2, 3]

for (const a of arr) {
  if (a <= 3) {
    arr.push(a + 3)
  }
  console.log(a)
}
```

ANSWER

The result would be numbers from 1 to 6. The *for...of* construct uses the
built-in iterator, which in turn would adapt to changes in the iterable object.

QUESTION 42

What would be the result of running the following code ?

```js
const obj = {}

Object.defineProperty(obj, 'a', {})
Object.defineProperty(obj, 'b', { get: () => {} })
Object.defineProperty(obj, 'c', { set: () => {} })

Object.getOwnPropertyNames(obj) // ?
```

ANSWER

The result would be `[ 'a', 'b', 'c' ]`. Accessor properties are also object's
own properties.

QUESTION 43

What would be the result of the following expression ?

```js
NaN ** 0
```

ANSWER

The ECMAScript spec states that if the exponent is `0`, then the result is `1`,
regardless of the base. This is contrary to the popular belief that: if an
expression includes *NaN*, then the result would be *NaN* too. This is also
contrary to what we might expect from mathematics and the so called
"indeterminate forms". Check this:
```
Infinity ** 0
```

QUESTION 44

What would be the result of the following expression ?

```js
JSON.parse('{}').__proto__
```

ANSWER

This will be `Object.prototype` of course. `JSON.__proto__` would yield the
same result.

QUESTION 45

What would be the result of running the following code ?

```js
function Func() {
  return new this.constructor()
}

new Func()
```

ANSWER

The code will throw with `RangeError` because of infinite constructor loop.

QUESTION 46

What would be the result of the following expressions ?

```js
// 1.
true + true // ?

// 2.
true + 1 // ?

// 3.
true + '1' // ?
```

ANSWER

The basic principle of the of the addition operator is as follows:
1. If any operand is a string, then convert to string and concatenate.
2. Otherwise convert to number and add.

Here is the result:
```js
// 1.
true + true // 2

// 2.
true + 1    // 2

// 3.
true + '1'  // 'true1'
```

QUESTION 47

What is the difference between `Function.prototype.call` and
`Function.prototype.apply` ?

ANSWER

Both functions invoke a function with a set of arguments. The small difference
lies in the fact that `call` accepts a *list* of arguments, but `apply` accepts
an *array* of arguments:
```js
func.call(this, 1, 2, 3)
func.apply(this, [1, 2, 3])
```

QUESTION 48

What is the purpose of function `Reflect.construct` ?

ANSWER

The function `Reflect.construct` is a way to call a constructor in the same way as the `new` keyword would do. The following are equivalent:
```js
// 1.
Reflect.construct(Func, [])

// 2.
new Func()
```

QUESTION 49

What is the difference between `Object.preventExtensions` and
`Reflect.preventExtensions` ?

ANSWER

The function `Reflect.preventExtensions` will throw if the argument is not an
object.

FINAL QUESTION

Why are all those questions so difficult ?

ANSWER

They are *not* difficult. Javascript is such a plain and simple language when
compared to C++ for example, no virtual functions, no protected variables,  no
function overloading, no destructors, no operator overloading, no friend
functions, no pointer casting, etc.

