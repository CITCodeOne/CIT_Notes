The lecture presented #JavaScript as a dynamically typed, runtime-evaluated language used in browsers and #Node.js. It covered values and types, #truthy / #falsy evaluation, #objects and #arrays, #destructuring, the #spread-operator, #functions and #closures, higher-order #array methods, #JSON, and #modules. The focus was on how these features support React: functional thinking, immutability, predictable data transformations, and module-based structure.

---

## #JavaScript Overview

JavaScript is a dynamically typed programming language where types belong to _values_, not variables. It runs directly in the browser and forms the execution layer of React applications.  
It supports prototype-based objects, first-class functions, closures, and lightweight modularization through imports/exports.  
Its flexibility allows fast development but also introduces pitfalls related to coercion, mutation, and runtime errors.

#### **What it covers**
- Executing JS in Node.js or the browser console
- #Values & #type-tags
- #Functions and #closures
- #Arrays, #objects, #JSON
- Pure array transformations
- ES modules

 **References**
- [JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [JavaScript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)

---
# #Values & #Types

JavaScript values have runtime-attached type tags such as `"number"`, `"string"`, `"object"`, and `"function"`.  
Because the language is dynamically typed, type checking happens _while running the program_ rather than during compilation. This allows flexibility but makes errors appear later.

(From [[2025-02-javascript.pdf]] 4–7)
#### **What it covers**
- Numbers (integers + floats combined)
- Strings
- Booleans
- `null`, `undefined`, `NaN`
- Objects and arrays
- Functions as first-class values

#### **Example**
```js
typeof 42        // "number"
typeof "hello"   // "string"
typeof {}        // "object"
typeof []        // "object"
typeof (()=>{})  // "function"
```

#### **Pitfalls**
- `"5" == 5` is `true` due to coercion
- Arrays are objects
- `null` reports type `"object"` (historic bug)

#### **Why it matters**
React relies heavily on correct type interpretation in conditional rendering, props, and state updates.

#### Reference
[MDN – Data Types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)

---

# #Truthy & #Falsy

## **What it is**

JavaScript allows any value to be tested as “true” or “false.” This flexibility enables concise conditions but can cause subtle bugs.

(From[[2025-02-javascript.pdf]] p. 9)

#### **What it covers**
**Falsy:** `false`, `0`, `""`, `undefined`, `null`, `NaN`  
**Truthy:** everything else.

#### **Example**

```js
if ("0") { } // runs, because "0" is truthy
```

#### **Pitfalls**
Assuming `"false"` or `"0"` behave as booleans.

#### **Why it matters**

Common pattern in JSX:

```jsx
{items.length && <List items={items} />}
```

**References**
[MDN – Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)  
[MDN – Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)

---
# #Arrays & #Objects

Arrays are ordered lists indexed from zero.  
Objects are collections of key–value pairs without requiring class declarations.

(From [[2025-02-javascript.pdf]] p. 11)

#### **What it covers**
- Heterogeneous arrays
- Object shape consistency
- Property access with dot notation

#### **Example**
```js
const user = { name: "Bob", age: 23 };
const users = [user, { name: "Mary", age: 8 }];
```

#### **Pitfalls**
Arrays and objects are mutable even when declared with `const`.

#### **Why it matters**
React state updates must avoid mutation:

```js
setUsers(prev => [...prev, newUser]);
```
 
**References**
[MDN – Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)  
[MDN – Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_objects)

---

# #Destructuring

A syntax pattern for extracting values from arrays and objects directly into variables. It reduces boilerplate and clarifies intent.

(From [[Lecture 02 - JavaScript]] p. 12 + Feature Summary)

#### **What it covers**
- Object destructuring
- Array destructuring
- Rest binding (`...rest`)
#### **Example**

```js
const { name, age } = user;
const [first, ...others] = ["one", "two", "three"];
```
#### **Pitfalls**
- Arrays destructure by _position_, not name
- Object destructuring requires exact property names

#### **Why it matters**
Common in React for props:
```jsx
function User({name, age}) { ... }
```

**References**
[MDN – Destructuring Assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

---

# #Spread Operator

The spread syntax (`...value`) expands arrays or objects into new arrays or objects.  
It is the fundamental tool for _immutable updates_ used in React.

(From [[2025-02-javascript.pdf]] p. 13 + Feature Summary) 
#### **What it covers**
- Copying
- Merging
- Passing function arguments
- Passing props
#### **Example**

```js
const updated = { ...user, age: 24 };
const numbers = [...arr, 10];
```
#### **Pitfalls**

Creates a shallow copy — nested objects still reference original data.
#### **Why it matters**

React state updates often depend on this exact pattern.

**References**
[MDN – Spread Syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

---

# #JSON

A lightweight text-based format for structured data. Combines objects, arrays, and primitive values into serializable text.

(From [[2025-02-javascript.pdf]] p. 14)

#### **What it covers**
- Serialization: `JSON.stringify()`
- Parsing: `JSON.parse()`
- API communication
#### **Example**
```js
const json = JSON.stringify(users);
const data = JSON.parse(json);
```

#### **Pitfalls**
Functions, undefined, and Dates do not serialize well.

#### **Why it matters**
All common APIs return JSON.  
React fetch workflows depend on this.

**References**
[MDN – JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)

---

# #let vs #const

`let` creates a mutable binding.  
`const` creates a binding that cannot be reassigned — but the internal structure can still mutate.

(From [[2025-02-javascript.pdf]] p. 15)
## **What it covers**
- Reassignment rules
- Immutable vs mutable bindings
- Prefer const in React
#### **Example**

```js
const user = { name: "Bob" };
user.name = "Arthur"; // allowed
```

#### **Pitfalls**

Believing that `const` makes the object immutable.

#### **Why it matters**

React encourages predictable patterns and often discourages reassigning variables.

#### **References**
[MDN – Declarations](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

---

# #Functions & #Closures

Functions are first-class values: they can be stored, passed, and returned.  
Closures occur when an inner function “remembers” variables from the outer scope.

(From [[2025-02-javascript.pdf]] pp. 16–19)

#### **What it covers**
- Function declarations
- Function expressions
- Arrow functions
- Higher-order functions
- Default parameters
- Rest parameters
#### **Example**
```js
const add = x => y => x + y;
const addFive = add(5);
addFive(2); // 7
```

#### **Pitfalls**
Closures can unintentionally depend on changing mutable state.

#### **Why it matters**
React hooks (useState, useEffect) rely deeply on closures.

**References**
[MDN – Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)  
[MDN – Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

---

# #Array Methods

Pure, higher-order methods for transforming arrays without mutating the original array.

(From [[2025-02-javascript.pdf]] pp. 20–25)
#### **What it covers**
- `.map()`
- `.filter()`
- `.every()`
- `.some()`
- `.find()`
- `.reduce()`
- `.slice()`
- `.toSorted()`
#### **Example**
```js
const odds = nums.filter(n => n % 2);
const doubled = nums.map(n => n * 2);
```
#### **Pitfalls**
- `.reduce()` can become unreadable
- Forgetting return values in callbacks
#### **Why it matters**
React list rendering relies on `.map()`:

```jsx
{users.map(u => <User key={u.id} {...u} />)}
```

**References**
[MDN – Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

---

# #Modules: import/export

#ES modules split functionality across files using `import` and `export`.  
React applications follow a one-component-per-file structure.

(From [[2025-02-javascript.pdf]] pp. 26–28)

#### **What it covers**
- `export default`
- Named exports
- Importing from local files (`./`)
- Component structure conventions
#### **Example**

```js
export default function Button() {}

import Button from "./Button";
```

#### **Pitfalls**
- Forgetting `./` in local imports
- Mixing default and named imports incorrectly
#### **Why it matters**
React bundlers (Vite, Babel) depend on correct module organization.

**References**
[MDN – import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)  
[MDN – export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

---
# Golden Notes
- JavaScript types belong to values, not variables.
- Truthy/falsy logic drives conditional rendering in React.
- Destructuring extracts structure cleanly and reduces boilerplate.
- Spread syntax enables immutable state updates.
- Pure array methods are essential for React list operations.
- Closures explain React’s hook behavior.
- ES modules create maintainable, component-based structures.