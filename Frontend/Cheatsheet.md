#React
- Is a modular, scalable, flexible, and popular front-end framework
- `createRoot()` from `react-dom/client` can be used to create a React root at the specified DOM element.
- A React root's `render()` method can be used to render JSX on screen.
- React updates only the parts of the DOM that changed, using the virtual DOM for efficiency. 
- Components are JavaScript functions that return JSX.
- Props allow components to receive data. They are read-only.
- State (via hooks like UseState) stores values that change over time and trigger re-renders.

#Javascript 
- Spaghetti code is code with unclear structure that is difficult to understand or maintain. 
- JavaScript is dynamically typed: values have types, variables do not. 
- Common value types: number, string, boolean, object, array, null, undefined, function. 
- `typeof`reveals the type tag of value.
- Automatic type conversion ( #coercion )
- `===`compares both value and type `==` converts types first.
- #truthy and #falsy determine how values behave in conditions. 
- `let`creates a variable, `const` creates a binding that cannot be reassigned.
- #Desctructuring and #spread (...) help unpack or copy data cleanly and immutably.
- #JSON is a text format for storing or sending data (`JSON.stringify(), JSON.parse()`).
- Array methods like `.map()`, `.filter()`, `.find()`, `.reduce()` create new arrays without modifying the original.

#JSX
- JSX is syntatic sugar: a readable way to write UI inside JavaScript.
- JSX is a JavaScript syntax extension that blends JS and HTML-like markup.
- JavaScript inside JSX uses `{ }` for values, variables, functions, arrays, and components.
- JSX elements look like HTML: `<h1>Hello</h1>` but compile to JavaScript function calls.
- JSX is an expression and can be stored in variables, passed to functions, or put into arrays.
- Attributes use HTML-like syntax but are actually JavaScript values `(use quotes or {})`.
- JSX must return one parent element, but you can wrap content in `<>...</>` (fragments). 
- Multiline JSX should be wrapped in parentheses.
- "Render" means to produce visible elements from JSX and show them on screen.
- Boolean props can be written as `admin(true)` or `admin={false}`. 
- Conditional rendering uses JavaScript operators: `&&, ||,` and the ternary condition `? a : b`. 
- Rendering lists requires a unique key prop for each element.
- JSX → JavaScript: React compiles JSX into function calls like `jsx()` and `jsxs()`


#HTML
- The #DOM (Document Object Model) is a tree representation of an HTML document.
- Each HTML element becomes a node that JavaScript (and React) can read and modify.
- React interacts with the DOM through a virtual DOM, making updates more efficient.
- HTML recap: browser reads HTML → builds the DOM → displays it.