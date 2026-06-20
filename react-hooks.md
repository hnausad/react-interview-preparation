React Hooks were introduced in React 16.8 to completely change how we write components. Before hooks, if you needed to use state or lifecycle methods, you *had* to write a Class Component.

**Hooks allow you to use state and other React features in functional components.** They make code cleaner, easier to read, and simpler to share logic between components without rewriting them.

---

## 1. The Core Rules of Hooks

Before diving into specific hooks, you must know the two strict rules React enforces. Breaking these will cause bugs or app crashes:

1. **Only Call Hooks at the Top Level:** Don't call Hooks inside loops, conditions, or nested functions. React relies on the *order* in which Hooks are called to preserve state correctly.
2. **Only Call Hooks from React Functions:** Call them from React functional components or from custom Hooks. Don't call them from regular JavaScript functions.

---

## 2. Deep Dive: The Essential Hooks

Here are the most vital hooks you will encounter, their use cases, and code examples.

### 1. `useState` (Managing Local State)

* **What it does:** Allows a functional component to hold and update its own local data.
* **Use Case:** Form inputs, tracking toggles (open/closed), counting items, loading states.

```jsx
import React, { useState } from 'react';

export default function Counter() {
  // Declare a state variable named "count", initialized to 0
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      {/* Update state using the setter function */}
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

```

### 2. `useEffect` (Handling Side Effects)

* **What it does:** Lets you synchronize a component with an external system. It replaces lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.
* **Use Case:** Fetching data from an API, setting up event listeners, timers, manual DOM updates.

```jsx
import React, { useState, useEffect } from 'react';

export default function DataFetcher() {
  const [data, setData] = useState([]);

  useEffect(() => {
    // 1. Side effect logic: Fetching data
    fetch('https://api.example.com/items')
      .then(res => res.json())
      .then(result => setData(result));

    // 2. Optional: Cleanup function (runs when component unmounts)
    return () => console.log('Component unmounted or dependencies changed');
  }, []); // 3. Dependency Array: Empty array means this runs ONCE on mount.

  return (
    <ul>
      {data.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  );
}

```

### 3. `useContext` (Global State Without Prop Drilling)

* **What it does:** Broadly shares data across a component tree without manually passing props down through every single level.
* **Use Case:** Dark/Light theme configuration, User authentication sessions, global language settings.

```jsx
import React, { createContext, useContext, useState } from 'react';

// 1. Create a Context
const ThemeContext = createContext();

export default function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={theme}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  return <ThemeButton />;
}

function ThemeButton() {
  // 2. Consume the context directly without accepting props from Toolbar
  const theme = useContext(ThemeContext);
  return <button className={theme}>Current Theme: {theme}</button>;
}

```

### 4. `useRef` (Persisting Values & DOM References)

* **What it does:** Creates a mutable object whose `.current` property persists across renders. Crucially, **changing a ref does NOT trigger a re-render**.
* **Use Case:** Accessing DOM nodes directly (focusing an input, playing media), keeping track of a timer ID.

```jsx
import React, { useRef } from 'react';

export default function FocusInput() {
  const inputRef = useRef(null);

  const handleClick = () => {
    // Access the direct DOM node and focus it
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus the Input Box</button>
    </div>
  );
}

```

---

## 3. Advanced Optimization Hooks

For scaling larger applications, React provides hooks focused purely on performance optimization:

* **`useMemo`:** Caches the **result of a complex calculation** so it isn't recalculated on every trivial re-render.
* **`useCallback`:** Caches a **function definition itself** to prevent breaking downstream optimizations (like child components wrapped in `React.memo`).
* **`useReducer`:** An alternative to `useState` built for complex local state management involving predictable, multi-step actions (similar to how Redux works).

---

## 4. High-Frequency Interview Questions

If you are preparing for a technical React interview, expect these core questions:

### Q1: What is the main difference between `useMemo` and `useCallback`?

* **Answer:** `useMemo` caches the **computed value** returned by a function, whereas `useCallback` caches the **actual instance of the function** itself.

### Q2: Why can't we call Hooks inside an `if` statement or a loop?

* **Answer:** React relies on the exact **call order** of Hooks on every single render to map state values to their corresponding hook variables. If you put a hook inside an `if` condition, and that condition changes to false, the subsequent hooks will execute out of order, misaligning React's internal state array and breaking the app.

### Q3: How do you replicate `componentDidMount` using hooks?

* **Answer:** You use the `useEffect` hook and provide an **empty dependency array (`[]`)**. This tells React that the side effect doesn't depend on any props or state, meaning it will run exactly once when the component initially mounts to the DOM.

### Q4: When would you use `useRef` instead of `useState`?

* **Answer:** You use `useRef` when you need to store data or track a value that needs to persist across renders, but **should not trigger a UI update** when it changes. You also use it to directly access and manipulate underlying HTML DOM elements.
