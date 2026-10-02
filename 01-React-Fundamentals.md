# 01 — React Fundamentals (MCQ) 🟢🟡

> Core concepts every interviewer expects you to know cold — in "What's the output?" MCQ style.

---

###### 1. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

- A: `3`
- B: `1`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

All three `setCount(count + 1)` calls use the **stale** `count` from the current render (`0`), so each computes `0 + 1 = 1`. React batches them into a single re-render. Use the updater form `setCount(c => c + 1)` to get `3`.

</details>

---

###### 2. What's the output?

```jsx
const element = <h1 className="title">Hi</h1>;
console.log(element.type);
```

- A: `"h1"`
- B: `"title"`
- C: `"Hi"`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

JSX compiles to `React.createElement('h1', { className: 'title' }, 'Hi')`. The returned element object has `type: 'h1'`, `props: { className: 'title', children: 'Hi' }`.

</details>

---

###### 3. What's the output?

```jsx
function Greeting() {
  return (
    <>
      <h1>Hello</h1>
      <h2>World</h2>
    </>
  );
}
```

How many DOM nodes does this render?

- A: 3
- B: 2
- C: 1
- D: 4

<details><summary><b>Answer</b></summary>

Answer: **B**

A fragment `<>...</>` does **not** create a DOM node. Only the two `<h1>` and `<h2>` elements appear in the DOM.

</details>

---

###### 4. What's the output?

```jsx
const users = ['A', 'B', 'C'];
users.map((u, i) => <li key={i}>{u}</li>);
```

Which statement is true?

- A: Using index as key is always safe
- B: Index keys cause bugs when the list reorders
- C: Keys are optional and have no effect
- D: Keys must be unique strings only

<details><summary><b>Answer</b></summary>

Answer: **B**

Index keys are unsafe when items can be reordered, inserted, or deleted — React reuses component state incorrectly. They're acceptable only for static, never-reordered lists.

</details>

---

###### 5. What's the output?

```jsx
function Child({ name }) {
  name = 'Changed';
  return <p>{name}</p>;
}

<Child name="Original" />
```

- A: `Original`
- B: `Changed`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

Props are read-only by convention, but nothing stops you from reassigning the local parameter. It renders `Changed`. This is a **bad practice** — never mutate props.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const [x, setX] = useState(1);
  console.log('render');
  return <button onClick={() => setX(2)}>Go</button>;
}
```

How many times does `render` log after clicking once?

- A: 0
- B: 1
- C: 2
- D: 3

<details><summary><b>Answer</b></summary>

Answer: **C** (in development with StrictMode)

`setX(2)` triggers a re-render. In development, React StrictMode **double-invokes** render functions to surface impurities, so `render` logs twice. In production it logs once.

</details>

---

###### 7. What's the output?

```jsx
const el = <MyComponent />;
console.log(el.type === MyComponent);
```

- A: `true`
- B: `false`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

For a component element, `element.type` is the component function itself. For a host element like `<div>`, `element.type` is the string `'div'`.

</details>

---

###### 8. What's the output?

```jsx
function App() {
  return (
    <div>
      <span>A</span>
      <span>B</span>
    </div>
  );
}
```

Which is true about reconciliation when `App` re-renders?

- A: React rebuilds the whole tree every time
- B: React keeps the `<div>` and only diffs children
- C: React always unmounts and remounts
- D: React throws an error

<details><summary><b>Answer</b></summary>

Answer: **B**

When element types match (`<div>` → `<div>`), React keeps the DOM node and only updates changed attributes/children. Different types cause a full teardown and rebuild.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

What does the button show after one click?

- A: `0`
- B: `1`
- C: `2`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

`setN(n + 1)` uses the current `n` (`0`) and schedules a re-render with `1`. The button shows `1`.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <button
      onClick={() => {
        setN(n + 1);
        setN(n + 1);
      }}
    >
      {n}
    </button>
  );
}
```

After one click, what does the button show?

- A: `1`
- B: `2`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

Both calls use the stale `n = 0`, so both compute `1`. React batches them → final state is `1`. Use `setN(prev => prev + 1)` twice to get `2`.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <button
      onClick={() => {
        setN(prev => prev + 1);
        setN(prev => prev + 1);
      }}
    >
      {n}
    </button>
  );
}
```

After one click, what does the button show?

- A: `1`
- B: `2`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The **updater form** receives the latest state. React queues both updates: `0 → 1 → 2`. Final state is `2`.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [obj, setObj] = useState({ a: 1 });
  obj.a = 2;
  setObj(obj);
  return <p>{obj.a}</p>;
}
```

- A: `1`
- B: `2`
- C: `Error`
- D: Nothing renders

<details><summary><b>Answer</b></summary>

Answer: **B**

Mutating state directly (`obj.a = 2`) then passing the same reference to `setObj` **won't trigger a re-render** (React bails out on identical reference), but the DOM already shows `2` because the object was mutated in place. This is a **bug** — always create a new object: `setObj({ ...obj, a: 2 })`.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  console.log('render');
  setN(0); // same value
  return <p>{n}</p>;
}
```

How many times does `render` log?

- A: 0
- B: 1
- C: 2
- D: Infinite loop

<details><summary><b>Answer</b></summary>

Answer: **B**

Setting state to the **same value** (via `Object.is` comparison) makes React **bail out** — it skips re-rendering. So `render` logs once (the initial render).

</details>

---

###### 14. What's the output?

```jsx
function App() {
  return <div className="box" />;
}
```

What is `element.props`?

- A: `{ className: 'box' }`
- B: `{ className: 'box', children: undefined }`
- C: `{ className: 'box', children: [] }`
- D: `{ className: 'box', children: null }`

<details><summary><b>Answer</b></summary>

Answer: **A**

For a self-closing element with no children, `props` is just `{ className: 'box' }`. React only adds a `children` key when there are children.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  return (
    <div>
      <span>Hi</span>
    </div>
  );
}
```

What is `element.props.children`?

- A: `"Hi"`
- B: `{ type: 'span', props: { children: 'Hi' } }`
- C: `["Hi"]`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

`children` is the element object for the `<span>`, which itself has `children: 'Hi'`. React nests element objects.

</details>

---

###### 16. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

Which best describes React's rendering after the click?

- A: React directly mutates the DOM button
- B: React builds a new VDOM, diffs it, and updates the DOM
- C: React re-runs the whole page
- D: React does nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

React creates a new Virtual DOM tree, reconciles it against the previous one, and applies only the minimal DOM changes (updating the button's text).

</details>

---

###### 17. What's the output?

```jsx
function App() {
  return <input value="hello" />;
}
```

What happens when the user types in the input?

- A: The value updates
- B: The value stays `"hello"` (controlled)
- C: An error is thrown
- D: The input becomes disabled

<details><summary><b>Answer</b></summary>

Answer: **B**

This is a **controlled** input with a fixed `value` and no `onChange`. React forces the value to stay `"hello"` — typing has no visible effect. Add `onChange` + state to make it editable.

</details>

---

###### 18. What's the output?

```jsx
function App() {
  return <input defaultValue="hello" />;
}
```

What happens when the user types?

- A: The value stays `"hello"`
- B: The user can type freely (uncontrolled)
- C: An error is thrown
- D: The input is read-only

<details><summary><b>Answer</b></summary>

Answer: **B**

`defaultValue` makes this an **uncontrolled** input — the DOM owns the value. The user can type freely; React doesn't control it.

</details>

---

###### 19. What's the output?

```jsx
function Parent() {
  const [temp, setTemp] = useState(20);
  return <Child temp={temp} />;
}

function Child({ temp }) {
  return <p>{temp}</p>;
}
```

This pattern is called:

- A: Prop drilling
- B: Lifting state up
- C: Context API
- D: Controlled component

<details><summary><b>Answer</b></summary>

Answer: **B**

State lives in the **closest common ancestor** (`Parent`) and is passed down as props — this is **lifting state up**, keeping `Child` controlled by the parent.

</details>

---

###### 20. What's the output?

```jsx
function App() {
  return (
    <React.StrictMode>
      <Child />
    </React.StrictMode>
  );
}
```

Which is true about StrictMode?

- A: It runs in production too
- B: It double-invokes renders in development
- C: It disables all hooks
- D: It adds a DOM wrapper

<details><summary><b>Answer</b></summary>

Answer: **B**

StrictMode is **development-only**. It double-invokes render functions and effects to surface impurities and missing cleanups. It has zero effect in production and adds no DOM node.

</details>

---

###### 21. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

Which library provides `useState`?

- A: `react-dom`
- B: `react`
- C: `babel`
- D: `vite`

<details><summary><b>Answer</b></summary>

Answer: **B**

`react` is the core library (components, hooks, elements). `react-dom` is the web renderer that mounts to the DOM.

</details>

---

###### 22. What's the output?

```jsx
function App() {
  return <div>Hello</div>;
}
```

What does Babel/esbuild do with this JSX?

- A: Nothing, browsers read JSX natively
- B: Transpiles it to `React.createElement('div', null, 'Hello')`
- C: Converts it to HTML
- D: Deletes it

<details><summary><b>Answer</b></summary>

Answer: **B**

Browsers can't read JSX. A compiler (Babel/esbuild/SWC) transpiles it into `React.createElement()` calls before it reaches the browser.

</details>

---

###### 23. What's the output?

```jsx
function App() {
  return <div>Hello</div>;
}
```

Which is a **pure** component?

- A: One that reads `Math.random()`
- B: One that returns the same JSX for the same props
- C: One that mutates a global variable
- D: One that writes to `localStorage`

<details><summary><b>Answer</b></summary>

Answer: **B**

A pure component returns the same output for the same input and has no side effects. Reading `Math.random()`, mutating globals, or writing to storage are all impure.

</details>

---

###### 24. What's the output?

```jsx
function App() {
  return <div>Hello</div>;
}
```

Which component name is **invalid** in React?

- A: `App`
- B: `MyComponent`
- C: `myComponent`
- D: `Header`

<details><summary><b>Answer</b></summary>

Answer: **C**

Component names must start with a **capital letter**. Lowercase names are treated as DOM tags (`<myComponent>` → `'myComponent'` string element, not a component).

</details>

---

###### 25. What's the output?

```jsx
function App() {
  return (
    <div>
      <span>A</span>
      <span>B</span>
    </div>
  );
}
```

If you change the outer `<div>` to a `<section>`, what does React do?

- A: Keeps the DOM and updates attributes
- B: Destroys the subtree and rebuilds it
- C: Throws an error
- D: Does nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

When the element **type changes** (`div` → `section`), React tears down the entire subtree and rebuilds it from scratch — state inside is lost.

</details>

---

###### 26. What's the output?

```jsx
function App() {
  const user = { name: 'React' };
  return <p>{user}</p>;
}
```

- A: `React`
- B: Nothing renders
- C: `Error: Objects are not valid as a React child`
- D: `[object Object]`

<details><summary><b>Answer</b></summary>

Answer: **C**

You can't render a plain object directly. Strings, numbers and arrays are OK, but objects throw `Objects are not valid as a React child`. Render `user.name` instead.

</details>

---

###### 27. What's the output?

```jsx
function App() {
  return (
    <div>
      {false}
      {null}
      {undefined}
      {true}
      Hello
    </div>
  );
}
```

What does the `<div>` show?

- A: `false null undefined true Hello`
- B: `Hello` only
- C: `Error`
- D: `0 Hello`

<details><summary><b>Answer</b></summary>

Answer: **B**

`false`, `null`, `undefined` and `true` all render **nothing**. This is why conditional rendering with `&&` and ternaries works.

</details>

---

###### 28. What's the output?

```jsx
function App({ count }) {
  return <div>{count && <p>Items: {count}</p>}</div>;
}

<App count={0} />
```

- A: Nothing renders
- B: `0` renders on screen
- C: `Items: 0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`0 && <p>...` evaluates to `0`, and React **renders** `0`. Classic `&&` pitfall. Fix: `{count > 0 && <p>...}` or `{!!count && ...}` or ternary.

</details>

---

###### 29. What's the output?

```jsx
function App() {
  return (
    <div class="box">
      Hello
    </div>
  );
}
```

- A: Renders fine with no issues
- B: Warning `Invalid DOM property 'class'. Did you mean 'className'?`
- C: `Error`
- D: Nothing renders

<details><summary><b>Answer</b></summary>

Answer: **B**

JSX uses `className` (since `class` is a JS reserved word). React warns and still applies the attribute, but in interviews the correct answer is: **always use `className`**.

</details>

---

###### 30. What's the output?

```jsx
function App() {
  return (
    <div>
      <label for="email">Email</label>
      <input id="email" />
    </div>
  );
}
```

- A: Works perfectly
- B: Warning — use `htmlFor` instead of `for`
- C: `Error`
- D: Label is not linked to input

<details><summary><b>Answer</b></summary>

Answer: **B**

`for` is reserved in JS. JSX uses `htmlFor`. React warns: `Invalid DOM property 'for'. Did you mean 'htmlFor'?`

</details>

---

###### 31. What's the output?

```jsx
function App() {
  return <div style="color: red">Hi</div>;
}
```

- A: Red text
- B: `Error` — `style` must be an object
- C: No style applied, no error
- D: Warning only

<details><summary><b>Answer</b></summary>

Answer: **B**

In JSX `style` takes an **object**, not a string: `style={{ color: 'red' }}`. Passing a string throws / warns and styles break. Double curlies = object inside expression.

</details>

---

###### 32. What's the output?

```jsx
function App() {
  return (
    <div>
      // This is a comment
      <p>Hi</p>
    </div>
  );
}
```

- A: Comment hidden, `Hi` renders
- B: `// This is a comment` renders as text
- C: `Error`
- D: Nothing renders

<details><summary><b>Answer</b></summary>

Answer: **B**

`//` inside JSX is **rendered as text**, not a comment. Use `{/* comment */}` for JSX comments.

</details>

---

###### 33. What's the output?

```jsx
function App() {
  return (
    <h1>Hello</h1>
    <p>World</p>
  );
}
```

- A: Renders both
- B: `Error: Adjacent JSX elements must be wrapped`
- C: Only `<h1>` renders
- D: Only `<p>` renders

<details><summary><b>Answer</b></summary>

Answer: **B**

A component must return **one parent element**. Wrap siblings in a `<div>`, `<section>` or fragment `<>...</>`.

</details>

---

###### 34. What's the output?

```jsx
const items = ['a', 'b'];
return (
  <>
    {items.map((item) => (
      <React.Fragment key={item}>
        <p>{item}</p>
      </React.Fragment>
    ))}
  </>
);
```

Can you use the short syntax `<>` with a `key` here?

- A: Yes, `< key={item}>` works
- B: No — short syntax cannot take `key`, use `<Fragment key>`
- C: Keys aren't needed for fragments
- D: `Error` either way

<details><summary><b>Answer</b></summary>

Answer: **B**

`<>...</>` cannot accept **any props**, including `key`. For keyed fragments in lists, import and use `<Fragment key={item}>`.

</details>

---

###### 35. What's the output?

```jsx
function Child({ key }) {
  return <p>{key}</p>;
}

function App() {
  return <Child key="my-key" />;
}
```

- A: `my-key`
- B: `undefined` + warning
- C: `Error`
- D: Empty `<p>`

<details><summary><b>Answer</b></summary>

Answer: **B**

`key` is **special** — React strips it and never passes it as a prop. `props.key` is always `undefined`. Need the value? Pass a different prop like `id="my-key"`.

</details>

---

###### 36. What's the output?

```jsx
function App() {
  return (
    <div>
      <button onClick={console.log('clicked')}>Go</button>
    </div>
  );
}
```

- A: Logs on every click
- B: Logs once during render, click does nothing
- C: `Error`
- D: Logs twice (StrictMode)

<details><summary><b>Answer</b></summary>

Answer: **B**

`onClick={console.log('clicked')}` **calls** the function during render instead of passing a reference. It logs immediately and assigns `undefined` as the handler. Fix: `onClick={() => console.log('clicked')}`.

</details>

---

###### 37. What's the output?

```jsx
function App() {
  return <button onclick="handleClick()">Go</button>;
}
```

- A: Calls `handleClick` on click
- B: Nothing happens on click + warning
- C: `Error`
- D: Works in production only

<details><summary><b>Answer</b></summary>

Answer: **B**

React events are **camelCase**: `onClick`, `onChange`, `onSubmit`. Lowercase `onclick` is treated as an unknown DOM attribute and never fires.

</details>

---

###### 38. What's the output?

```jsx
function App() {
  return (
    <form onSubmit={(e) => console.log('submitted')}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

What happens on submit?

- A: Logs + page reloads
- B: Logs only, no reload
- C: `Error`
- D: Nothing happens

<details><summary><b>Answer</b></summary>

Answer: **A**

Without `e.preventDefault()`, a form submit triggers a full **page reload**. Correct: `onSubmit={(e) => { e.preventDefault(); ... }}`. This is the #1 forms gotcha in 2026 fresher interviews.

</details>

---

###### 39. What's the output?

```jsx
function App() {
  let count = 0;
  return <button onClick={() => { count++; console.log(count); }}>{count}</button>;
}
```

After clicking, what does the button show?

- A: `1`
- B: `0` (UI never updates, console shows 1)
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

Mutating a plain variable **doesn't trigger a re-render**. The console logs `1` but the UI still shows `0`. Only `setState`, parent re-render, or context change re-renders. Use `useState`.

</details>

---

###### 40. What's the output?

```jsx
function App() {
  const [user, setUser] = useState({ name: 'A', age: 20 });
  return <button onClick={() => setUser({ age: 21 })}>Birthday</button>;
}
```

After click, what is `user`?

- A: `{ name: 'A', age: 21 }`
- B: `{ age: 21 }` — `name` is lost
- C: `{ name: 'A', age: 20 }`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useState` **replaces** state, it doesn't merge (unlike class `this.setState`). Fix: `setUser(prev => ({ ...prev, age: 21 }))`.

</details>

---

###### 41. What's the output?

```jsx
function expensive() {
  console.log('expensive ran');
  return 42;
}

function App() {
  const [n] = useState(expensive());
  return <p>{n}</p>;
}
```

How many times does `expensive ran` log on every re-render?

- A: Once total
- B: On every render
- C: Never
- D: Twice

<details><summary><b>Answer</b></summary>

Answer: **B**

`useState(expensive())` **calls** `expensive()` on every render (only the first result is used). For lazy init, pass a function: `useState(() => expensive())` — runs once.

</details>

---

###### 42. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <button onClick={() => {
      setTimeout(() => setN(n + 1), 100);
      setTimeout(() => setN(n + 1), 100);
    }}>
      {n}
    </button>
  );
}
```

In React 18+, after clicking, what does the button show?

- A: `2`
- B: `1` (both timeouts use stale `n`)
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Even in timeouts/promises, React 18 **auto-batches**. Both callbacks close over the same stale `n = 0`, so both set `1`. Fix with updater: `setN(prev => prev + 1)`.

</details>

---

###### 43. What's the output?

```jsx
function Child() {
  console.log('child render');
  return <p>Child</p>;
}

function App() {
  const [n, setN] = useState(0);
  return (
    <div>
      <button onClick={() => setN(n + 1)}>+</button>
      <Child />
    </div>
  );
}
```

After clicking `+`, does `child render` log?

- A: No
- B: Yes — parent re-render re-renders all children by default
- C: Only in StrictMode
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

By default, when a parent re-renders, **all children re-render** even if their props didn't change. Optimize with `React.memo`, `useMemo`, or colocating state.

</details>

---

###### 44. What's the output?

```jsx
function App({ show }) {
  if (!show) return null;
  return <p>Hello</p>;
}

<App show={false} />
```

- A: `Error`
- B: Empty — `null` renders nothing
- C: `Hello`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

Returning `null` from a component renders **nothing** — valid for conditional rendering / early returns. Different from returning `undefined`, which throws.

</details>

---

###### 45. What's the output?

```jsx
function App() {
  return <p>{Math.random()}</p>;
}
```

Is this component pure?

- A: Yes
- B: No — same props can give different output
- C: Only in StrictMode
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Pure components return the **same JSX for the same props** with no side effects. `Math.random()`, `Date.now()`, mutating globals, or fetching in render are all **impure** and break memoization + StrictMode checks.

</details>

---

###### 46. What's the output?

```jsx
function App() {
  const data = fetch('/api/user').then(r => r.json());
  return <p>{data.name}</p>;
}
```

- A: Renders user name
- B: Broken — fetching during render + promise rendered as child
- C: `Error: Objects are not valid`
- D: Both B and C

<details><summary><b>Answer</b></summary>

Answer: **D**

Never fetch **during render** — it fires on every render, returns a Promise (can't render promises as children in client components), and causes waterfalls. Fetch in `useEffect`, event handlers, or use a library / Server Component.

</details>

---

###### 47. What's the output?

```jsx
function App() {
  return (
    <div>
      <input defaultValue="hi" />
      <input value="hi" onChange={() => {}} />
    </div>
  );
}
```

Which input is controlled?

- A: First
- B: Second
- C: Both
- D: Neither

<details><summary><b>Answer</b></summary>

Answer: **B**

`value` + `onChange` = **controlled** (React owns state). `defaultValue` = **uncontrolled** (DOM owns state, React only sets initial value). A `value` with no `onChange` is read-only.

</details>

---

###### 48. What's the output?

```jsx
// React 18+
import { createRoot } from 'react-dom/client';
createRoot(document.getElementById('root')).render(<App />);
```

Why `createRoot` instead of `ReactDOM.render`?

- A: Just a rename, no difference
- B: Enables concurrent features (batching, Transitions, Suspense)
- C: Faster HTML parsing
- D: Required for class components

<details><summary><b>Answer</b></summary>

Answer: **B**

`ReactDOM.render` is legacy (React 17). React 18's `createRoot` enables the **concurrent renderer** — automatic batching, `useTransition`, `useDeferredValue`, streaming Suspense. Old API warns in React 18.

</details>

---

###### 49. What's the output?

```jsx
function App() {
  const [show, setShow] = useState(false);
  return (
    <div>
      {show ? <Input /> : <Input />}
      <button onClick={() => setShow(!show)}>Toggle</button>
    </div>
  );
}

function Input() {
  const [text, setText] = useState('');
  return <input value={text} onChange={(e) => setText(e.target.value)} placeholder="type..." />;
}
```

You type `hello`, then click Toggle. What happens to the text?

- A: Text is preserved
- B: Text is cleared
- C: `Error`
- D: Input unmounts

<details><summary><b>Answer</b></summary>

Answer: **A**

Same position + same type (`<Input>` → `<Input>`) = React **preserves state**. If you changed the type (`<input>` → `<textarea>`) or added a different `key`, state would reset. This is reconciliation 101.

</details>

---

###### 50. What's the output?

```jsx
function App() {
  const [show, setShow] = useState(true);
  return (
    <div>
      {show ? <input placeholder="A" /> : <textarea placeholder="B" />}
      <button onClick={() => setShow(!show)}>Switch</button>
    </div>
  );
}
```

You type in the input, then click Switch. What happens to the typed text + focus?

- A: Text and focus preserved
- B: Text lost, focus lost — different type rebuilds DOM
- C: `Error`
- D: Nothing changes

<details><summary><b>Answer</b></summary>

Answer: **B**

Different element **type** at the same position (`input` → `textarea`) makes React **tear down and rebuild** the DOM node. State, text, and focus are lost. Same rule applies to `<div>` → `<section>`.

</details>

---

###### 51. What's the output?

```jsx
const list = [{ id: 1 }, { id: 2 }, { id: 3 }];
// sorted / reversed later
list.map(item => <Item key={item.id} id={item.id} />);
```

Why is `key={item.id}` better than `key={index}`?

- A: It's faster to type
- B: Stable IDs preserve component state correctly on reorder/insert/delete
- C: Index keys throw errors
- D: No difference

<details><summary><b>Answer</b></summary>

Answer: **B**

Index keys shift when the list reorders — React reuses the **wrong** component state (e.g. typed input values jump rows). Stable unique IDs let React track each item correctly. Index is OK only for static, never-reordered lists.

</details>

---

###### 52. What's the output?

```jsx
<ul>
  <li key="a">A</li>
  <li key="a">B</li>
</ul>
```

- A: Works fine
- B: Warning — duplicate keys, UI may bug on updates
- C: `Error`
- D: Only first renders

<details><summary><b>Answer</b></summary>

Answer: **B**

Keys must be **unique among siblings** (not globally). Duplicates don't throw but break diffing — updates/reorders render the wrong items. Always ensure sibling-unique keys.

</details>

---

###### 53. What's the output?

```jsx
function Child({ name }) {
  return <p>{name}</p>;
}

// React 19
function App() {
  return <Child name="React" ref={myRef} />;
}
```

- A: `Error` — must use `forwardRef`
- B: Works — `ref` is a normal prop in React 19
- C: `ref` is `undefined`
- D: Warning only

<details><summary><b>Answer</b></summary>

Answer: **B**

React 19 made `ref` a **regular prop** — no `forwardRef` needed for function components: `function Child({ ref, ...props })`. Asked in every 2025–2026 interview covering React 19.

</details>

---

###### 54. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);
  const handleClick = () => setCount(count + 1);
  return <MemoChild onClick={handleClick} />;
}

const MemoChild = React.memo(function MemoChild({ onClick }) {
  return <button onClick={onClick}>+</button>;
});
```

Does `memo` prevent re-renders when `App` re-renders for other reasons?

- A: Yes, always
- B: No — `handleClick` is a new function every render
- C: Only in production
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`React.memo` shallow-compares props. An inline `handleClick` is a **new reference** each render, so memo always fails. Fix with `useCallback(() => setCount(c => c + 1), [])`.

</details>

---

###### 55. What's the output?

```jsx
function App() {
  return (
    <MyLibrary.Card>
      <MyLibrary.Button>Click</MyLibrary.Button>
    </MyLibrary.Card>
  );
}
```

- A: `Error` — component names can't contain dots
- B: Valid — dot notation for namespaced components
- C: Only works with classes
- D: Renders nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

Dot notation like `<MyLibrary.Card />` is **valid** — still capitalized, so React treats it as a component. Common in design systems (`<Form.Input />`, `<Table.Row />`).

</details>

---

###### 56. What's the output?

```jsx
function App() {
  const el = <div className="box">Hi</div>;
  console.log(typeof el);
  console.log(Array.isArray(el));
}
```

- A: `object`, `false`
- B: `string`, `false`
- C: `object`, `true`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

A JSX element is a **plain object** (`{ type, props, key, ... }`), not a DOM node and not an array. `React.createElement` returns this description object; `react-dom` turns it into real DOM.

</details>

---

###### 57. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const handleClick = () => setN(0); // same value
  return <button onClick={handleClick}>{n}</button>;
}
```

After clicking 3 times, how many re-renders?

- A: 3
- B: 0 extra — React bails out on identical state
- C: Infinite loop
- D: 1

<details><summary><b>Answer</b></summary>

Answer: **B**

React compares with `Object.is`. Setting the **same value** bails out — no re-render scheduled. Note: bailout still renders once in some edge cases (props changed), but same-state `setState` is cheap.

</details>

---

###### 58. What's the output?

```jsx
function App() {
  return (
    <div>
      <h1>Title</h1>
      {5 + 5}
      {'Hello' + ' World'}
      {[1, 2, 3].map(n => <span key={n}>{n}</span>)}
    </div>
  );
}
```

- A: `Error` — only one expression allowed
- B: Renders `Title 10 Hello World 123`
- C: Renders nothing
- D: `Error` — arrays can't render

<details><summary><b>Answer</b></summary>

Answer: **B**

`{}` can hold **any JS expression**: arithmetic, strings, arrays (with keys). JSX is just syntax sugar — everything inside `{}` is evaluated.

</details>

---

###### 59. What's the output?

```jsx
function GrandParent() {
  const [theme, setTheme] = useState('dark');
  return <Parent theme={theme} />;
}
function Parent({ theme }) {
  return <Child theme={theme} />;
}
function Child({ theme }) {
  return <p>{theme}</p>;
}
```

Passing `theme` through `Parent` just to reach `Child` is called:

- A: Lifting state up
- B: Prop drilling
- C: Composition
- D: Context API

<details><summary><b>Answer</b></summary>

Answer: **B**

**Prop drilling** = passing props through intermediate components that don't use them. Fix with composition (`children`), Context, or state managers. Top 2026 interview definition question.

</details>

---

###### 60. What's the output?

```jsx
function App() {
  return <p>{'React is a framework'}</p>;
}
```

Is this statement true?

- A: Yes
- B: No — React is a **library** for UI, not a full framework
- C: Depends on version
- D: True since React 19

<details><summary><b>Answer</b></summary>

Answer: **B**

React is a **library** (UI layer only — no router, no HTTP, no build system built-in). Frameworks like Next.js / Remix build **on top** of React. Classic HR + technical screening question.

</details>

---

**Next:** [02 — Components & Props →](./02-Components-and-Props.md)
