The lecture focused on introducing the core parts of JavaScript, needed to build user interfaces, especially in React. It covers how JavaScript handles values and types, how functions work, how arrays and objects are used to structure data, and how modern features like destructuring, spread and array methods make code cleaner and easier to work with.

It also introduces JSON for data exhange and shows how import/export is used to structure code across files. Overall, it provides the basic JavaScript tools needed for writing clear, modern frontend applications.

## Values & Types

JavaScript is built on a small set of core value types. Unlike C# or Java, the type is not tied to the variable but to the value itself. This makes JavaScript flexible, but it also means that you have to pay attention to what type you are working with in runtime. 

Values fall into categories like numbers, strings, booleans, arrays, objects, functions, null, and undefined. Many of these behave as expected, but JavaScript will often try to convert values for you if it thinks it makes sense. 

`typeof` helps with inspecting the type tag of any value. 

```js
typeof 42        // "number"
typeof []        // "object"
typeof "hello"   // "string"
typeof null      // "object"  (quirk of JavaScript)
```
A key details is that arrays, objects, and even functions are all considered "objects" under the hood. 

## Automatic Type Conversion (Coercion)
JavaScript will often convert values automatically. It does this during arithmetic, comparisons, and string concatention. This behaviour is known s *coercion*, and it is one of the biggest differences between JavaScript and statically typed languages. 

For example:

```js
5 * "4"     // 20   (string converted to number)
5 + "4"     // "54" (number converted to string)
true * 5    // 5    (true becomes 1)
```
when you use `==`, in JavaScript will try to convert both sides to comparable values.
When using `===`, it does not convert anything, and simply compares both value and type directly.
```js
5 == "5"    // true  (converted)
5 === "5"   // false (different types)
```
Using `===` avoids surprises and is always recommended. 

## Truthy and Falsy
Instead of using `true` and `fales`, JavaScript treats some values as *false-like* when used in conditions. 

These are called falsy values, and they behave as `false`:
`false, 0, "", null, undefined, NaN`

Everything else is truthy, meaning it behaves as `true`

This is important because logical operators (`&& ||`) return the actual value, not just true/false.

This is often used for fallbacks: 
```js
const username = "" || "Guest"; 
// "Guest"
```
Understanding truthiness helps reading compact JavaScript code and write cleaner conditions. 

## Let and const 
JavaScript offers two main ways to declare variables: `let` and `const`. 

`let` allows reassignment.

`const` does not allow reassignment of the variable binding, but the value itself may stillc hange if it is an object or array.
```js
const user = {name:"Bob"};
user.name = "Alice"; // allowed (the object changed)
```
Because of this, many developers prefer `const` as the default, for safety, and use `let` only when they genuienly need to reassign a value. 

## Arrays and Objects
Arrays and objects are the core data structures in JavaScript. Arrays hold ordered values, while objects map named keys to values. 

Arrays can contain different types, but it is best practice to keep them consistent for clarity. 
```js
const list = ["a", "b", "c"];
list[1]  // "b"
```

Objects are simply flexible containers
```js
const user = {name:"Bob", age:23};
user.name  // "Bob"
```
Objects do not require classes like in Java — you create them directly using `{}`.
This makes JavaScript fast to work with but also places responsibility on you to keep object shapes consistent. 

## Descruturing and Spread
Desctructuring allows you to extract values directly from arrays or objects in a clean and readable way. 

This is extremely common in modern JavaScript and React.
```js 
const user = {name:"Bob", age:23};
const {name, age} = user;
```
This spread operator `(...)` lets you copy or merge arrays and objects without mutating the original data. 

This is crucial in React because state must remain immuntable 
```js
const arr = [1,2,3];
const extended = [...arr, 4];   // [1,2,3,4]
```
Spread and destructuring helps with writing shorter, more expressive code. 

## JSON
JSON is a text format used to store and transfer structured data. 
Even though it's called JavaScript Object Notation, it is used across nearly all programming languages. 

Use `JSON.stringify()` to convert a JavaScript object into a text string.
Use `JSON.parse()` to convert text back into a data structure.
```js
const data = JSON.parse('{"name":"Bob"}');
// {name:"Bob"}
```
This is fundamental for working with APIs.

## Functions
Functions in JavaScript behave like values. You can store them in variables, pass them as arguments, or return them from other functions. This makes JavaScript highly flexible and supports functional programming patterns. 

There are multiple ways to define a function, but arrow functions are the most common, especially in React.
```js
const add = (a,b) => a + b;
```

Functions can be stored, passed, returned 

**Function declarations**
```js
function add(a,b) {
  return a+b;
}
```
**Function expression**
```js
const add = function(a,b) {
  return a+b;
};
```
**Arrow function**
```js
const add = (a,b) => a + b;
```

**Closures**
When a function returns another function, the inner function "remembers" the surrounding variables. 

This is called a closure.
```js
function makeAdder(x) {
  return y => x + y;
}

const add5 = makeAdder(5);
add5(3)  // 8
```
Closures are central concept in JavaScript, even if you don't always think about them explicitly. 

## Default and Rest Parameters
JavaScript lets you define default values for function parameters, which prevents undefined behaviour. 
```js
function inc(x, step = 1) {
  return x + step;
}
```
Rest parameters allow functions to take any number of arguments. 
```js
function sum(...values) {
  return values.reduce((a,b) => a+b, 0);
}
```
This makes JavaScript functsion (yet again) very flexiable and expressive.

## Array Methods
Modern JavAScript favours pure array methods rather than traditional loops. 
These methods return new arrays, which works perfectly with React's immutable design. 

They each focus on different operations

**every**
Checks if all elements match a condition 
```js
[2,3,5].every(n => n > 1)  // true
```

**some**
Checks if at least one matches
```js
[1,3,5].some(n => n % 2 === 0)  // false
```

**find**
Returns all matching elements 
```js
[1,2,3,4].filter(n => n > 2)  // [3,4]
```
**filter**
Returns all matching elements
```js
[1,2,3,4].filter(n => n > 2)  // [3,4]
```
**map**
Transforms each element
```js
[1,2,3].map(n => n * 10)  // [10,20,30]
```
**reduce**
Combines all elements into one value
```js
[1,2,3].reduce((sum, n) => sum + n, 0)  // 6
```
**slice**
Copies a section of an array

**toSorted**
Returns a shorted copy without modifying the original 

**Chaining example**

This shows how readable JavaScript becomes when using these methods
```js
const result =
  [1,2,3,4,5]
    .filter(n => n > 2)
    .map(n => n * 10)
    .reduce((a,b) => a + b, 0);

// 120
```
This approach is central to modern JavaScript and React development.

| <center>Method</center> | <center>Purpose</center> |
| ----------------------- | ------------------------ |
| `every(f)`              | all match?               |
| `some(f)`               | at least one match?      |
| `find(f)`               | first matching element   |
| `filter(f)`             | all matching elements    |
| `map(f)`                | transform each element   |
| `reduce(g, init)`       | combine all elements     |
| `slice(i, j)`           | copy subarry             |
| `toSorted()`            | sorted copy              |


## Import and Export
JavaScript Modules let you split code across files so it stays organized

**Default exports**
Use this when the file represents one main thing (like a React component)
```js
export default function Button() {}
import Button from "./Button";
```

**Name exports**
Useful for utilities or multiple exports from the same file.
```js
export const pi = 3.14;
export function double(x) { return x * 2; }

import {pi, double} from "./math";
```
React typically use a mix: components are default exports, while helper functions are named exports. 

**Remember!!**
Local imports must start with `"./"`
JSX files end with `.jsx`

