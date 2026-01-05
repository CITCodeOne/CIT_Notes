**React state makes components interactive.**  
Use `useState` for values that change, `useEffect` for code that runs because of those changes, and `useContext` to share data without prop-drilling. State lives in the nearest common parent, changes trigger re-renders, and effects handle side tasks like fetching or timers.

### State in React

State is information that changes over time and makes React re-render a component.  
Stateless components only depend on props, while stateful components keep internal values.

Data flows downward in React. A parent can send data to children, but children cannot send state upward. When multiple components need the same state, it must live in their nearest common ancestor.

React has three main kinds of state:  
Application data such as user info.  
UI state such as whether a tab is open.  
Form data such as input values.

Constant data and duplicates are not considered state.

**Summary**
- State = data that changes over time and causes the UI to update
- Props = data passed into the component
- State = data owned by component. 

*From slides*
- Stateless components only depend on props 
- Stateful components remember something between renders
- Data only flows downward (parent → child)

**Two main kinds of state**
(React.dev)
1. Application state (global)
	1. example: logged-in user, shopping cart.
2. UI state (local)
	1. example: "is menu open?", "which tab is active?"
3. Form state (stored locally in form)
	1. example: input text, checkbox values

**Not state**: constant data, duplicates, or multiple/different versions of the same info. 

### useState

useState creates a state value and a setter.

Example:

```jsx
const [count, setCount] = useState(0);
```

React ignores the initial value after the first render, so heavy initialization should be passed as a function.  
Setting state does not update immediately. If the next value depends on the previous, use an updater function.

Example:

```jsx
setCount(c => c + 1);
```

Avoid large compound objects unless needed. Always create a new object instead of modifying an existing one.

*From slides*
`useState(init)` gives us:
- Current value
- a setter function

Example (from react.dev)
```js
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count is {count}
    </button>
  );
}
```

**Important rules**
- Changing state schedules a re-render; it does not update instantly 
- If the new value depends on the old one, use a function: `setCount(c => c + 1)`
- the `init` value only runs on the first render.
	- Use lazy initialization for heavy work: `useState(() => expensiveSetup())
- Don't store big ojects/combined structures unless needed — update becomes more expensive.

#### Where to Place State

Because data flows down, state should be placed at the highest component that needs it.  
A parent can pass down both the current value and the setter.

*From slides*
React emphasize:
- Data flows down
- If multiple components need the same state → put it in their nearest common parent.

So, for:
App
↳ Child A
↳ Child B

If both need the same value, state must be in **App**.

### useEffect

useEffect lets a component run code after rendering.

Three typical patterns:  
Run on every render: `useEffect(fn)`.  
Run once on mount: `useEffect(fn, []).`  
Run when dependencies change: `useEffect(fn, [value]).`

Effects are used for fetching data, updating the document title, timers, subscriptions, and initializing state from props.

Cleanup functions run before the next effect or when unmounting.

Example:

```jsx
useEffect(() => {
  const id = setInterval(() => setCount(c => c + 1), 1000);
  return () => clearInterval(id);
}, []);
```

Avoid returning promises from useEffect. The linter warns if dependencies are missing. Functions used inside an effect should often be placed inside the effect to avoid infinite loops.

**Summary**
`useEffect` lets you run code when the component:
1. first renders
2. re-renders
3. when some state/prop changes
4. unmounts (cleanup)

*General forms*
```js
useEffect(() => {
  // run on *every* render
});

useEffect(() => {
  // run once on mount
}, []);

useEffect(() => {
  // run when x changes
}, [x]);
```

*From slides*

Fetching data when a state changes
```js
useEffect(() => {
  fetch("url?results=" + count)
    .then(res => res.json())
    .then(data => setUsers(data.results));
}, [count]);
```

Setting up an interval (with cleanup)
```js
useEffect(() => {
  const id = setInterval(() => {
    setCount(c => c + 1);
  }, 2500);

  return () => clearInterval(id);
}, []);
```

**Tips**
- Don't return promises from `useEffect` — the return value must be a cleanup function.
- Linter may warn a function is referenced but not in the dependency array.
- To avoid infinite loops, define functions inside the effect if necessary.

### Prop Drilling

Sometimes many components pass along a value they do not use.  
This complicates component trees and makes code harder to maintain.

When a value must be passed through multiple components that don't use it.

**Example**
App → Layout → Wrapper → Button
But only Button needs theme.

### useContext - Avoid prop drilling

useContext solves prop drilling by allowing components to read shared values without passing them manually.

Create a context, provide the value at a high level, and read it anywhere below.

Example:

```jsx
const ThemeContext = createContext("red");

<ThemeContext.Provider value="blue">
  <App />
</ThemeContext.Provider>;

const theme = useContext(ThemeContext);
```

Context is best for values that rarely change, such as a theme or authenticated user.


So in short
`UserContext` lets you share data through a "context provider" at the top level.

**Steps**

1. Create context
```js
export const ThemeContext = createContext("red");
```

2. Provide the value
```js
<ThemeContext.Provider value="blue">
  <App />
</ThemeContext.Provider>
```

3. Use the value
```js
const theme = useContext(ThemeContext);

return <button style={{ color: theme }}>Click</button>;
```

This way, middle components don't need to receive or pass props.

### Example App (uses useState + useEffect + useContext)

Complete example

```jsx
import { useState, useEffect, useContext, createContext } from "react";

// 1: Create a context for theme
const ThemeContext = createContext("blue");

export default function App() {
  return (
    <ThemeContext.Provider value="purple">
      <Counter />
    </ThemeContext.Provider>
  );
}

function Counter() {
  const theme = useContext(ThemeContext);     // Using context
  const [count, setCount] = useState(0);      // Using state
  const [time, setTime] = useState(0);

  // Using effect: timer updates every second
  useEffect(() => {
    const id = setInterval(() => setTime(t => t + 1), 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <div style={{ color: theme }}>
      <p>Count: {count}</p>
      <p>Timer: {time}s</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}
```

This demonstrates:
State (count), an effect (timer), and context (themes).


### Visual Diagrams (ASCII-style)

#### Data flow in React

```
Parent (state)
   |
   | props
   v
Child → cannot send data back up
```

#### Where to place state

```
      App
     /   \
  A        B
     ^   ^
     |   |
Both need the same data → state moves up to App
```

#### useEffect lifecycle

```
Render → Effect runs → (later) Cleanup → Effect runs again
```

#### Context flow

```
Provider
   |
   | (invisible chain)
   v
Any child can access this value
```


- State makes components interactive by triggering re-renders.
- useState gives you a value and a setter; use functional updates when needed.
- Put state in the nearest common parent of components that need it.
- useEffect handles side effects: mount, update, unmount.
- Always clean up intervals, listeners, and timers.
- Prop drilling passes props through components that do not use them.
- useContext removes prop drilling by sharing values globally.