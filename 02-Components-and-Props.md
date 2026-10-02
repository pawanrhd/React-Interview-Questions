# 02 — Components & Props (MCQ) 🟢🟡

> How components are composed, how props flow, and the gotchas interviewers love.

---

###### 1. What's the output?

```jsx
function Child({ name }) {
  return <p>{name}</p>;
}

function App() {
  return <Child name="React" />;
}
```

- A: `React`
- B: `undefined`
- C: `Error`
- D: `name`

<details><summary><b>Answer</b></summary>

Answer: **A**

`name` is passed as a prop and destructured in `Child`. It renders `React`.

</details>

---

###### 2. What's the output?

```jsx
function Child(props) {
  return <p>{props}</p>;
}

<Child name="React" />
```

- A: `React`
- B: `[object Object]`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

`props` is an object `{ name: 'React' }`. Rendering an object directly coerces it to the string `[object Object]`.

</details>

---

###### 3. What's the output?

```jsx
function Child({ name = 'Default' }) {
  return <p>{name}</p>;
}

<Child />
```

- A: `Default`
- B: `undefined`
- C: `Error`
- D: `null`

<details><summary><b>Answer</b></summary>

Answer: **A**

Default parameters kick in when the prop is `undefined`. `name` becomes `'Default'`.

</details>

---

###### 4. What's the output?

```jsx
function Child({ name = 'Default' }) {
  return <p>{name}</p>;
}

<Child name={null} />
```

- A: `Default`
- B: `null`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

Default values only apply when the prop is `undefined`. Passing `null` explicitly keeps `null`.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  return <Child>Hello</Child>;
}

function Child({ children }) {
  return <p>{children}</p>;
}
```

- A: `Hello`
- B: `undefined`
- C: `Error`
- D: `[object Object]`

<details><summary><b>Answer</b></summary>

Answer: **A**

Content between a component's tags is passed as the special `children` prop.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  return <Child a={1} b={2} />;
}

function Child(props) {
  return <p>{Object.keys(props).length}</p>;
}
```

- A: `1`
- B: `2`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`props` contains `{ a: 1, b: 2 }` — two keys.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const el = <Child />;
  return <p>{el.type === Child}</p>;
}
```

- A: `true`
- B: `false`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

For a component element, `element.type` is the component function itself, so it equals `Child`.

</details>

---

###### 8. What's the output?

```jsx
function App() {
  return <Child />;
}

function Child() {
  return <GrandChild />;
}

function GrandChild() {
  return <p>Deep</p>;
}
```

This is an example of:

- A: Prop drilling
- B: Composition
- C: Context API
- D: HOC

<details><summary><b>Answer</b></summary>

Answer: **B**

Nesting components inside each other is **composition** — building complex UIs from small reusable pieces.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  return <Child name="A" />;
}

function Child({ name }) {
  return <p>{name}</p>;
}
```

Can `Child` modify `name` and have it reflect in `App`?

- A: Yes, props are mutable
- B: No, props are read-only
- C: Only with `useState`
- D: Only with `useRef`

<details><summary><b>Answer</b></summary>

Answer: **B**

Props are **read-only**. A child cannot modify its props. To change data, the parent must own state and pass down callbacks.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);
  return <Child count={count} onIncrement={() => setCount(count + 1)} />;
}
```

The `onIncrement` prop is an example of:

- A: Prop drilling
- B: Lifting state up
- C: Callback prop (child → parent communication)
- D: Context

<details><summary><b>Answer</b></summary>

Answer: **C**

Passing a function as a prop lets the **child** notify the **parent** — the standard way to communicate upward.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  return <Child {...{ a: 1, b: 2 }} />;
}

function Child(props) {
  return <p>{props.a + props.b}</p>;
}
```

- A: `3`
- B: `12`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

The spread operator `{...{ a: 1, b: 2 }}` passes `a` and `b` as props. `1 + 2 = 3`.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  return <Child />;
}

function Child() {
  return <p>Hi</p>;
}
```

How many times does `Child` render when `App` re-renders?

- A: 0
- B: 1
- C: 2
- D: Depends on memoization

<details><summary><b>Answer</b></summary>

Answer: **B**

By default, when a parent re-renders, all children re-render too (unless wrapped in `React.memo`). So `Child` renders once per parent render.

</details>

---

###### 13. What's the output?

```jsx
const Child = React.memo(function Child({ name }) {
  return <p>{name}</p>;
});

function App() {
  const [n, setN] = useState(0);
  return (
    <>
      <Child name="Static" />
      <button onClick={() => setN(n + 1)}>+</button>
    </>
  );
}
```

When `n` changes, does `Child` re-render?

- A: Yes
- B: No, `React.memo` skips it
- C: Only in dev
- D: Error

<details><summary><b>Answer</b></summary>

Answer: **B**

`React.memo` shallow-compares props. Since `name` is unchanged, `Child` skips re-rendering even though the parent re-renders.

</details>

---

###### 14. What's the output?

```jsx
const Child = React.memo(function Child({ onClick }) {
  return <button onClick={onClick}>Go</button>;
});

function App() {
  const [n, setN] = useState(0);
  return <Child onClick={() => setN(n + 1)} />;
}
```

Does `React.memo` help here?

- A: Yes, it always skips re-renders
- B: No — the inline arrow function is a new reference each render
- C: It throws an error
- D: Only in production

<details><summary><b>Answer</b></summary>

Answer: **B**

`React.memo` does a **shallow** comparison. An inline arrow function `() => setN(...)` is a **new reference** every render, so the memo check fails and `Child` re-renders. Fix with `useCallback`.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  return <Child />;
}

function Child() {
  return <p>Hi</p>;
}
```

What is the correct way to pass a `key` to a list of `Child`?

- A: `<Child key={i} />` inside a map
- B: `<Child key="static" />` always
- C: Keys are not needed
- D: `<Child id={i} />`

<details><summary><b>Answer</b></summary>

Answer: **A**

Use a stable, unique `key` when rendering lists via `map`. `key` is special — it's not passed to the component as a prop.

</details>

---

###### 16. What's the output?

```jsx
function App() {
  return <Child name="A" name="B" />;
}
```

- A: `A`
- B: `B`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

Duplicate props — the **last** one wins. `name` becomes `'B'`. (JSX may warn in some setups.)

</details>

---

###### 17. What's the output?

```jsx
function App() {
  return <Child name="A" {...{ name: 'B' }} />;
}
```

- A: `A`
- B: `B`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

The spread comes **after**, so it overrides the earlier `name="A"`. Order matters — later props win.

</details>

---

###### 18. What's the output?

```jsx
function App() {
  return <Child name="A" {...{ name: 'B' }} />;
}
```

If the spread came **before** `name="A"`, what would render?

- A: `A`
- B: `B`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

`<Child {...{ name: 'B' }} name="A" />` — the explicit `name="A"` comes last and wins.

</details>

---

###### 19. What's the output?

```jsx
function App() {
  return <Child />;
}

function Child() {
  return <p>Hi</p>;
}
```

Which is the correct way to define a component that takes no props?

- A: `function Child() {}`
- B: `function Child(props) {}`
- C: Both A and B are valid
- D: Neither

<details><summary><b>Answer</b></summary>

Answer: **C**

Both are valid. If you don't use props, you can omit the parameter entirely.

</details>

---

###### 20. What's the output?

```jsx
function App() {
  return <Child name="React" />;
}

function Child({ name }) {
  return <p>{name}</p>;
}
```

What happens if `Child` is defined **after** `App` in the file?

- A: Error — hoisting issue
- B: Works fine (function declarations hoist)
- C: `undefined`
- D: Only works with arrow functions

<details><summary><b>Answer</b></summary>

Answer: **B**

Function declarations are **hoisted**, so order doesn't matter. (Arrow function components assigned to `const` are NOT hoisted — they must be defined before use.)

</details>

---

**Next:** [03 — State & Lifecycle →](./03-State-and-Lifecycle.md)
