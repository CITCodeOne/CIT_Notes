
This lecture covered how JSX works as a syntax layer on top of JavaScript, how HTML and the DOM work, how React transforms JSX into JavaScript function calls, and how React renders user interfaces using the virtual DOM. The session demonstrates how components, JSX expressions, attributes, lists, and conditions are translated and evaluated, and how React efficiently updates the actual DOM through reconciliation.

---

# 1. #JSX Basics

JSX is a syntax extension that lets you write HTML-like markup directly inside JavaScript.  
It is _not_ HTML, _not_ a string, and _not_ valid JavaScript on its own. Tools like Babel or React’s build system transform JSX into pure JavaScript calls (`jsx()` or `jsxs()`).

JSX makes UI structure easier to read and allows combining markup and JavaScript expressions in one place.

(See Lecture page 3–7)  
(See JSX summary section 1–3)  
[React.dev – Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx)

#### **What it covers**
- Basic JSX element syntax
- Capitalized vs lowercase tags
- Fragments
- Embedding JavaScript in `{}`
- Self-closing tags
- JSX grammar extension to JavaScript grammar
#### **Examples**

```jsx
<h1>Hello</h1>
<Logo />
<>
  <Nav />
  <Main />
</>
```
#### **Pitfalls**
- JSX elements must always be closed
- Capitalized = component, lowercase = HTML
- JSX inside `return` must be wrapped in parentheses to avoid ASI mistakes

#### **Why it matters**
JSX is the standard way to describe UI in React. It expresses structure declaratively, letting React handle DOM updating and diffing.

#### **MDN / React.dev References**

[React – Describing the UI](https://react.dev/learn/describing-the-ui)  
[React – JSX](https://react.dev/learn/writing-markup-with-jsx)

---

# 2. #Embedding JavaScript in JSX

Curly braces `{}` inside JSX allow embedding JavaScript expressions into markup.  
Only **expressions** are allowed, not statements (`if`, `for`, etc.).

(See Lecture p. 8)  
[React.dev – JavaScript in JSX with Curly Braces](https://react.dev/learn/javascript-in-jsx-with-curly-braces)

#### **What it covers**
- Render strings, numbers, elements
- Arrays render element-by-element
- Falsy values render nothing (except `0`)

#### **Examples**

```jsx
<p>{23}</p>
<p>{user.name}</p>
<p>{["Bob", <Logo />]}</p>
```

#### **Pitfalls**
- `{false}`, `{null}`, `{undefined}` render nothing
- Arrays must have `key` when rendered with `.map()`
#### **Why it matters**
JSX dynamically interleaves UI with logic, making it crucial to understand which values render and which disappear.

---

# 3. JSX #Attributes & #Props
Attributes in JSX look like HTML attributes, but accept JavaScript expressions via `{}`.  
They become **props** when used on components.

(See Lecture p. 9)  
(See JSX Summary section 4)
#### **What it covers**
- Static text attributes
- Expression attributes
- Passing booleans, numbers, and functions
- Inline style objects
- Spread attributes `{...obj}`
#### **Examples**

```jsx
<img src="bob.png" />
<img src={IMGDIR + "bob.png"} />

<User name="Bob" age={23} admin />

<button onClick={() => alert("Hi")} />

<p style={{ backgroundColor: "#f00" }} />
<User {...user} />
```
#### **Pitfalls**
- `class` → `className`
- `for` → `htmlFor`
- Event handlers must be camelCase
- Passing `"false"` instead of `{false}` leads to truthy behavior
#### **Why it matters**
Props are the primary way components receive data.

**References**
[React – Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx)

---

# **4. Conditional Rendering
JSX allows #conditionally-rendering UI using expression-based JavaScript: logical operators or the ternary operator.

(See Lecture p. 10)  
(See JSX Summary section 6)
## **What it covers**
- Logical AND (`&&`)
- Logical OR (`||`)
- Ternary (`condition ? A : B`)
- Recognizing that `if` is _not_ allowed inside JSX

#### **Examples**

```jsx
{loggedIn && <LogoutButton />}
{avatar || <DefaultAvatar />}
{cart.length === 1 ? "item" : "items"}
```

#### **Pitfalls**
- `0` is falsy but _will render_
- `condition && value` can accidentally render unexpected values if value is not JSX

**Why it matters**
Declarative UI depends heavily on expressing conditions inside markup.

---

# 5. #Lists & #Keys
#Rendering lists in JSX by mapping an array to elements requires unique keys so React can reconcile changes correctly.

(See Lecture pp. 15–16)  
[React.dev – Rendering Lists](https://react.dev/learn/rendering-lists)

#### **What it covers**
- Mapping data arrays to UI elements
- Keys for reconciliation
- Keys must be stable, unique, and not indices
- Arrays render element-by-element
#### **Examples**

```jsx
users.map(u =>
  <User key={u.id} name={u.name} age={u.age} />
)
```

#### **Pitfalls**
- Using array index as key
- Not providing a key at all
- Arrays rendered directly require all elements to have keys

Why it matters
React uses keys to track element identity during virtual DOM diffing.

---

# 6. #HTML, #DOM, and the Browser

HTML describes the structure of a webpage.  
The DOM is the browser’s in-memory tree representation of that document.  
React interacts with the DOM indirectly.

(See Lecture pp. 17–18)  
[MDN – What is a DOM Tree?](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model#what_is_a_dom_tree)

#### **What it covers**
- HTML file structure
- DOM node types
- JavaScript manipulating the DOM directly (`innerHTML`)
- Separation between HTML source and DOM representation
#### **Examples**
(Lecture HTML example on p. 17 shows structure and attributes)

#### **Pitfalls**
Manual DOM manipulation is error-prone, imperative, and not scalable for UI apps.

#### **Why it matters**
React abstracts DOM updates so developers focus on state → UI mapping.

---

# 7. React Rendering & Virtual DOM 
React renders JSX into a #virtual-DOM (an in-memory representation), compares it to the previous version, and updates only the parts of the real #DOM that changed.

(See Lecture pp. 20–23)  
[React.dev – Understanding UI as a Tree](https://react.dev/learn/understanding-your-ui-as-a-tree)  
[React.dev – Render and Commit](https://react.dev/learn/render-and-commit)

#### **What it covers**
- `createRoot()` and `.render()`
- How JSX becomes JavaScript (`jsx()` / `jsxs()`)
    - Example shown on Lecture p. 21
- React’s rendering algorithm
    - Re-render virtual DOM
    - Diff new vs old
    - Commit changes to real DOM

#### **Example**

```jsx
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

#### **Pitfalls**
- Assuming React re-renders the entire DOM
- Forgetting that state or props changes trigger re-rendering

#### **Why it matters**

React’s efficiency and declarative approach depend on the virtual DOM and reconciliation.

---

# 8. Translating JSX to JavaScript #JSXCompilation
JSX is compiled into function calls like `jsx()` or `jsxs()` (depending on children count).  
It’s pure JavaScript after transformation.

(See Lecture p. 21)

## **What it covers**
- How `<h1>Hello</h1>` becomes `jsx('h1', {...})`
- Children become arrays
- Style attributes become object literals

#### **Example** _(from lecture)_

JSX:
```jsx
<h1 style={{color: "red"}}>
  Hello
  <Big bold>mighty</Big>
  {getName(who)}
</h1>
```

Compiled JavaScript:
```js
jsxs("h1", {
  style: { color: "red" },
  children: [
    "Hello",
    jsx(Big, { bold: true, children: "mighty" }),
    getName(who)
  ]
});
```

#### **Why it matters**
Understanding compilation helps debug JSX errors and see how React interprets UI structure.

---

# **Literature List**
### **React Documentation**
- [Describing the UI](https://react.dev/learn/describing-the-ui)
- [Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx)
- [JavaScript in JSX with Curly Braces](https://react.dev/learn/javascript-in-jsx-with-curly-braces)
- [Rendering Lists](https://react.dev/learn/rendering-lists)
- [Understanding Your UI as a Tree](https://react.dev/learn/understanding-your-ui-as-a-tree)
- [Render and Commit](https://react.dev/learn/render-and-commit)
### **MDN Documentation**
- [What is a DOM Tree?](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model#what_is_a_dom_tree)
### **Lectures**
- _Lecture 03 – JSX & Rendering_
- _JSX & React Components Summary_

---

# **One-Line Golden Notes**
- JSX is a syntax layer that compiles into pure JavaScript function calls.
- JSX expressions must be expressions, not statements.
- Props are attributes that pass data to components.
- JSX conditions use `&&`, `||`, or ternaries because `if` is not allowed.
- Lists require stable, unique keys to support React’s reconciliation.
- React manages DOM updates by diffing virtual DOM trees.
- JSX must compile to JavaScript before React can render it.