# 05 — Rendering & Performance (MCQ) 🟡🔴

> Reconciliation, batching, memoization, and how React decides what to update.

---

###### 1. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  console.log('App render');
  return (
    <div>
      <button onClick={() => setN(n + 1)}>{n}</button>
      <Child />
    </div>
  );
}
```

After clicking once, what happens?

- A: Only `Child` re-renders
- B: `App` re-renders → `Child` re-renders too (unless memoized)
- C: Nothing
- D: Only the button re-renders

<details><summary><b>Answer</b></summary>

Answer: **B**

State lives in `App`, so `App` re-renders. By default, **children re-render with the parent** unless wrapped in `React.memo`.

</details>

---

###### 2. What's the output?

```jsx
const Child = React.memo(function Child({ n }) {
  console.log('child');
  return <p>{n}</p>;
});

function App() {
  const [n, setN] = useState(0);
  console.log('app');
  return <Child n={n} />;
}
```

After clicking to increment, log order?

- A: `app`, `child`
- B: `app` only
- C: `child` only
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **A**

`n` changes → `React.memo` shallow compare **fails** → `Child` re-renders. Memo skips only when props are **shallow-equal**.

</details>

---

###### 3. What's the output?

```jsx
const Child = React.memo(function Child({ onClick }) {
  console.log('child');
  return <button onClick={onClick}>+</button>;
});

function App() {
  const [n, setN] = useState(0);
  return <Child onClick={() => setN(n + 1)} />;
}
```

Does `child` log on every `App` render?

- A: No — memo always works
- B: Yes — inline arrow is a new reference each render
- C: Only in dev
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`React.memo` does a **shallow** prop compare. The inline function is a brand-new object every render → memo fails. Fix: `useCallback` (with correct deps).

</details>

---

###### 4. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const value = useMemo(() => ({ double: n * 2 }), [n]);
  return <Child value={value} />;
}
```

Why memoize `value`?

- A: It's not needed — objects are shared by default
- B: New object literal each render would break `React.memo` on `Child`
- C: To avoid errors
- D: To speed up `n * 2`

<details><summary><b>Answer</b></summary>

Answer: **B**

`{ double: n * 2 }` creates a **new reference** every render → memoized child sees changed props. `useMemo` keeps identity stable while deps don't change.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  setTimeout(() => setN(1), 0);

  return <p>{n}</p>;
}
```

What happens?

- A: Infinite loop (setTimeout in render)
- B: Schedules a re-render — and loops forever calling render again
- C: Renders `1` once
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Side effects like `setTimeout` in the **render body** re-fire on every render → infinite render loop. Move it into `useEffect`.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    setTimeout(() => setN(1), 0);
  }, []);

  return <p>{n}</p>;
}
```

- A: Infinite loop
- B: Renders `0`, then `1` once — effect runs once
- C: Renders `1` only
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Effect runs once on mount (due to `[]`), schedules one update. `n = 1` doesn't re-trigger the effect. Correct pattern.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  React.startTransition(() => {
    setN(n + 1);
  });

  return <p>{n}</p>;
}
```

- A: `Error` — must be in event handler
- B: Marks update as low priority (Transition) — UI stays responsive
- C: Freezes the app
- D: Skips render

<details><summary><b>Answer</b></summary>

Answer: **B**

`startTransition` marks state updates as **non-urgent** — React can interrupt them, keeping clicks/typing responsive. Used with expensive lists or deferred rendering.

</details>

---

###### 8. What's the output?

```jsx
function App() {
  const [query, setQuery] = useState('');
  const deferred = useDeferredValue(query);

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ExpensiveList search={deferred} />
    </div>
  );
}
```

What does `useDeferredValue` do?

- A: Debounces the input
- B: Lets urgent input update immediately while `ExpensiveList` gets a stale value first
- C: Caches results
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The input (urgent) updates instantly. The heavy list (deferred) renders with the **previous** value, then catches up — keeping typing smooth.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  console.log(n);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

You click 3 times. How many times does `n` log?

- A: 1
- B: 3 (initial + updates) → logs `0, 1, 2, 3` = 4
- C: 4
- D: 0

<details><summary><b>Answer</b></summary>

Answer: **C**

Render runs on mount (`0`) and after each click (`1`, `2`, `3`) → **4 logs**. Each render gets its own closure.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [list, setList] = useState([1, 2, 3]);

  function add() {
    setList([...list, 4]);
  }

  return <button onClick={add}>Add</button>;
}
```

Why `[...list, 4]` instead of `list.push(4)`?

- A: `push` is faster
- B: `push` mutates state in place — React may not detect the change
- C: `push` throws
- D: Both are the same

<details><summary><b>Answer</b></summary>

Answer: **B**

React compares state references. `push` keeps the **same array** → bailout (no re-render) or subtle bugs. Always create a **new** array/object for updates.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const expensive = useMemo(() => heavyCompute(n), [n]);
  return <p>{expensive}</p>;
}
```

You click a button that sets an **unrelated** state. Does `heavyCompute` re-run?

- A: Yes — any re-render recomputes
- B: No — deps `[n]` unchanged, memo holds value
- C: Only in StrictMode
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useMemo` recomputes only when **deps change**. Unrelated re-renders reuse the cached result.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const value = useMemo(() => expensive(n), [n]);
  return <p>{value}</p>;
}
```

When should you **not** use `useMemo`?

- A: Never — always memoize
- B: For cheap operations or values only used once (overhead > benefit)
- C: In dev only
- D: With lists

<details><summary><b>Answer</b></summary>

Answer: **B**

Memoization costs memory + comparison time. Only memoize **expensive** computations or objects/arrays whose **identity** matters for child props / effects.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  return (
    <>
      <Child1 />
      <Child2 />
      <Child3 />
      <button onClick={() => setN(n + 1)}>+</button>
    </>
  );
}
```

After clicking, how many components re-render?

- A: Only `App`
- B: `App` + all three children (unless memoized)
- C: Just the button
- D: None

<details><summary><b>Answer</b></summary>

Answer: **B**

Parent state update re-renders the parent and **all** children by default. Memoize stable children to skip them.

</details>

---

###### 14. What's the output?

```jsx
function Parent() {
  const [n, setN] = useState(0);
  const ctx = useContext(SomeContext);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

If the context **value** changes but `n` doesn't, does `Parent` re-render?

- A: No
- B: Yes — context consumers re-render on value change
- C: Only with `useMemo`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Components reading a context via `useContext` re-render whenever the provider's `value` changes — independent of their own state.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  console.log('render', n);

  if (n > 10) {
    return <p>Too big</p>;
  }

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

You click until `n = 11`. What renders?

- A: `<p>Too big</p>` — early return skips rest
- B: `<p>Too big</p>` + button
- C: `Error`
- D: Button shows `11`

<details><summary><b>Answer</b></summary>

Answer: **A**

Once `n > 10`, the function hits `return <p>...` before the button. This is **conditional rendering via early return**.

</details>

---

###### 16. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useLayoutEffect(() => {
    console.log('layout');
  }, [n]);

  useEffect(() => {
    console.log('effect');
  }, [n]);

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

Log order after a click?

- A: `layout`, `effect`
- B: `effect`, `layout`
- C: `layout` only
- D: `effect` only

<details><summary><b>Answer</b></summary>

Answer: **A**

`useLayoutEffect` runs **synchronously before paint**, `useEffect` runs **after paint**. Always `layout` → `effect`.

</details>

---

###### 17. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const handle = () => {
    setN(1);
    setN(2);
    setN(3);
  };

  return <button onClick={handle}>{n}</button>;
}
```

- A: `3`
- B: `1`
- C: Re-renders 3 times
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

React 18 **batches** all three into one render with the **last** value: `3`.

</details>

---

###### 18. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  Promise.resolve().then(() => setN(1));

  return <p>{n}</p>;
}
```

How many renders?

- A: 1
- B: 2 (React 18 batches the microtask too)
- C: 3
- D: 0

<details><summary><b>Answer</b></summary>

Answer: **B**

React 18 auto-batches **including** promises, timeouts, native events. Initial render `0`, then one batched update → `1`.

</details>

---

###### 19. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    console.log('effect');
  }, [n]);

  return <button onClick={() => setN(0)}>{n}</button>;
```

You click when `n` is already `0`. Does the effect re-run?

- A: Yes — every setState triggers effects
- B: No — same value, React bails out, no render
- C: Only in StrictMode
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`Object.is(old, new)` equal → React **bails out** (no re-render, no effects).

</details>

---

###### 20. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  console.log('A');
  setN(1);
  console.log('B');

  return <p>{n}</p>;
}
```

- A: Logs `A`, `B`; state becomes `1` but no infinite loop
- B: Infinite loop — setState during render
- C: `Error`
- D: Logs `A` only

<details><summary><b>Answer</b></summary>

Answer: **A**

Calling `setState` during render with the **same component** is allowed only as an "adjust state during render" pattern (and must be conditional/limited). Unconditional `setN(1)` with `n=0` runs once → next render `n=1`, `setN(1)` bails. But it warns if it loops — always prefer event handlers/effects.

</details>

---

**Next:** [06 — React Router →](./06-React-Router.md)
