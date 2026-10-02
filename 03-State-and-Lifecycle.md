# 03 — State & Lifecycle (MCQ) 🟢🟡

> How state behaves, when components mount/unmount, and the lifecycle gotchas.

---

###### 1. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(count);
  }, []);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

What does `useEffect` log on mount and after a click?

- A: `0` then `1`
- B: `0` only
- C: `0`, `1`, `2` on each change
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

Empty dependency array `[]` means the effect runs **once on mount**. It captures the initial `count = 0` and never re-runs, so it logs `0` only. Include `count` in deps to log on every change.

</details>

---

###### 2. What's the output?

```jsx
useEffect(() => {
  console.log('run');
  return () => console.log('cleanup');
}, []);
```

In React 18 StrictMode dev, what's the log order on mount?

- A: `run`, `cleanup`, `run`
- B: `run` only
- C: `run`, `run`
- D: `cleanup`, `run`

<details><summary><b>Answer</b></summary>

Answer: **A**

StrictMode **double-invokes** effects on mount: run → cleanup → run. This exposes missing cleanup functions. In production it runs once: `run`.

</details>

---

###### 3. What's the output?

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <p>{count}</p>;
}
```

What does the counter show after 3 seconds?

- A: `3`
- B: `1` (stale `count = 0` captured)
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The interval closure captures `count = 0` once (empty deps). Every tick computes `0 + 1 = 1`. Fix: `setCount(c => c + 1)` (updater) or add `count` to deps (re-creates interval — usually wrong).

</details>

---

###### 4. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('effect', count);
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

Log order after clicking once?

- A: `effect 0` then `effect 1`
- B: `effect 1` only
- C: `effect 0` only
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **A**

On mount: `effect 0`. After the click, `count` changes to `1`, deps differ, effect re-runs: `effect 1`. Effects run **after** paint.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  const [show, setShow] = useState(true);

  useEffect(() => {
    return () => console.log('unmount');
  }, []);

  return show ? <Child /> : <p>gone</p>;
}
```

When you toggle `show` to `false`, when does `unmount` log?

- A: Never
- B: When `Child` unmounts (cleanup)
- C: On mount
- D: Before render

<details><summary><b>Answer</b></summary>

Answer: **B**

The cleanup returned from `useEffect` runs when the component **unmounts** (or before the effect re-runs). This is where you clear intervals, subscriptions, and event listeners.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  useEffect(() => {
    console.log('mounted');
  }, []);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

After 5 clicks, how many times does `mounted` log?

- A: 5
- B: 1
- C: 6
- D: 0

<details><summary><b>Answer</b></summary>

Answer: **B**

Effects with `[]` run once on mount. Re-renders don't re-run them.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setCount(count + 1);
  }, []);

  return <p>{count}</p>;
}
```

- A: Renders `1`
- B: Renders `0`
- C: Infinite loop
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A** (then stops)

Setting state in the effect causes one re-render → `count = 1`. The effect has `[]` deps, so it doesn't re-run. Infinite loops happen only if the effect sets state **and** re-runs (deps change each render, like a new object/array literal).

</details>

---

###### 8. What's the output?

```jsx
function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api')
      .then(r => r.json())
      .then(d => setData(d));
  }, []);

  return <p>{data ? data.name : 'Loading...'}</p>;
}
```

What renders on first paint?

- A: `Loading...`
- B: `data.name`
- C: `Error`
- D: Blank

<details><summary><b>Answer</b></summary>

Answer: **A**

`data` starts as `null`, so `Loading...` renders. The fetch resolves later → `setData` triggers a re-render with `data.name`. This is the **fetch-in-effect** pattern.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    fetch(`/api/count/${count}`).then(...);
  }, [count]);
```

Every keystroke/counter change triggers a fetch. What's the recommended fix?

- A: Remove the dependency array entirely
- B: Wrap `count` logic in `useTransition` / debounce, or only fetch when needed
- C: Move fetch to render body
- D: Use `useMemo`

<details><summary><b>Answer</b></summary>

Answer: **B**

Changing deps re-runs the effect each time — fine for correctness but can spam requests. Debounce, use `useTransition`, or move fetching to an event handler / Server Component.

</details>

---

###### 10. What's the output?

```jsx
useEffect(() => {
  const handler = () => console.log('resize');
  window.addEventListener('resize', handler);
}, []);
```

What's missing?

- A: Dependency on `handler`
- B: Cleanup — `return () => window.removeEventListener('resize', handler)`
- C: `async` keyword
- D: Nothing — this is correct

<details><summary><b>Answer</b></summary>

Answer: **B**

Without cleanup, the listener **leaks** on unmount/re-render. Always return a cleanup that removes listeners, clears timers, aborts fetches.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <button onClick={() => setCount(c => c + 1)}>+</button>;
}
```

This is a side effect that:

- A: Runs during render
- B: Runs after paint when `count` changes
- C: Runs only on mount
- D: Never runs

<details><summary><b>Answer</b></summary>

Answer: **B**

Effects run **after** the browser paints. `useEffect` is for synchronizing with external systems (DOM, APIs, subscriptions). `useLayoutEffect` runs synchronously before paint.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);
  useLayoutEffect(() => {
    console.log('layout');
  }, [count]);
  console.log('render');
  return <p>{count}</p>;
}
```

Log order after a change?

- A: `render`, `layout`
- B: `layout`, `render`
- C: `render` only
- D: `layout` only

<details><summary><b>Answer</b></summary>

Answer: **A**

Render body runs first, then `useLayoutEffect` fires **before paint** (synchronously after DOM mutations). `useEffect` would fire after paint. Use layout effect only when measuring DOM before paint to avoid flicker.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [items, setItems] = useState([]);

  useEffect(() => {
    if (items.length === 0) {
      setItems(['a', 'b']);
    }
  }, [items]);

  return <ul>{items.map(i => <li key={i}>{i}</li>)}</ul>;
}
```

- A: Renders items, no loop
- B: Infinite loop
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B** (in some cases) — actually **A** here

The guard `if (items.length === 0)` prevents re-setting once filled, so it settles. **But** the classic unguarded version (`setItems([...])` every run with a new array) loops forever because a new array reference changes deps. Always guard or use initial state.

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [time, setTime] = useState(Date.now());

  useEffect(() => {
    const id = setInterval(() => setTime(Date.now()), 1000);
    return () => clearInterval(id);
  }, []);

  return <p>{time}</p>;
}
```

- A: Updates every second correctly
- B: Freezes after first second
- C: `Error`
- D: Memory leak

<details><summary><b>Answer</b></summary>

Answer: **A**

`setTime` gets a fresh value each tick (not derived from stale state), interval is cleaned up on unmount. Correct pattern for ticking clocks.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('effect');
    return () => console.log('cleanup');
  }, [count]);

  return <button onClick={() => setCount(c => c + 1)}>+</button>;
}
```

Log order after clicking once (after mount)?

- A: `cleanup`, `effect`
- B: `effect`, `cleanup`
- C: `effect` only
- D: `cleanup` only

<details><summary><b>Answer</b></summary>

Answer: **A**

When deps change, React runs the **old cleanup first**, then the new effect. On unmount, only `cleanup` runs.

</details>

---

###### 16. What's the output?

```jsx
class App extends React.Component {
  state = { n: 0 };

  componentDidMount() {
    console.log('mount');
  }

  componentDidUpdate(prevProps, prevState) {
    console.log('update', prevState.n, this.state.n);
  }

  render() {
    return <button onClick={() => this.setState({ n: this.state.n + 1 })}>{this.state.n}</button>;
  }
}
```

After one click, log order?

- A: `update 0 1`
- B: `mount`
- C: `mount`, `update 0 1`
- D: `update 0 1`, `mount`

<details><summary><b>Answer</b></summary>

Answer: **C**

Lifecycle: `componentDidMount` on mount, then `componentDidUpdate(prev, curr)` after every update (here `0 → 1`).

</details>

---

###### 17. What's the output?

```jsx
class App extends React.Component {
  state = { open: true };

  render() {
    return this.state.open ? <Panel /> : null;
  }
}
```

When `open` becomes `false`, which lifecycle runs in `Panel`?

- A: `componentDidMount`
- B: `componentWillUnmount`
- C: `componentDidUpdate`
- D: None

<details><summary><b>Answer</b></summary>

Answer: **B**

When a component is removed from the tree, `componentWillUnmount` fires — the class equivalent of the `useEffect` cleanup.

</details>

---

###### 18. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  // NOT recommended
  const double = n * 2;

  return <p>{double}</p>;
}
```

Should `double` be stored in state?

- A: Yes, derived values belong in state
- B: No — derive during render; state is for data that changes independently
- C: Only with `useMemo`
- D: Only in class components

<details><summary><b>Answer</b></summary>

Answer: **B**

`n * 2` is **derived** — compute it during render (or with `useMemo` if expensive). State is for values that change on their own (user input, subscriptions, server data).

</details>

---

###### 19. What's the output?

```jsx
function App() {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);

  function handleBoth() {
    setA(1);
    setB(1);
  }

  return <button onClick={handleBoth}>Go</button>;
}
```

How many re-renders after clicking?

- A: 2
- B: 1 (React 18 batches both)
- C: 3
- D: 0

<details><summary><b>Answer</b></summary>

Answer: **B**

React 18 **auto-batches** all updates — including in promises, timeouts, and native handlers. Both state updates flush in **one** re-render. (React 17 batched only inside React events.)

</details>

---

###### 20. What's the output?

```jsx
function App() {
  const [items, setItems] = useState(['a']);

  function add() {
    setItems([...items, 'b']);
    setItems(prev => [...prev, 'c']);
  }

  return <button onClick={add}>Add</button>;
}
```

What does `items` become after clicking?

- A: `['a', 'b', 'c']`
- B: `['a', 'c']` (first update overwritten)
- C: `['a', 'b']`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

The first call sets `['a','b']`; the second (updater) receives that result and appends `'c'`. Updaters chain correctly — mixed styles still work as long as later calls use `prev`.

</details>

---

**Next:** [04 — Hooks Deep Dive →](./04-Hooks-Deep-Dive.md)
