# 04 — Hooks Deep Dive (MCQ) 🟡🔴

> useState, useEffect, useMemo, useCallback, useRef, useContext, and the rules interviewers quiz you on.

---

###### 1. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  }

  return <button onClick={increment}>{count}</button>;
}
```

After one click, what does the button show?

- A: `3`
- B: `1`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

All three calls use the **stale** `count = 0` → each computes `1`. React batches them into one update. Use `setCount(c => c + 1)` three times to get `3`.

</details>

---

###### 2. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
  }

  return <button onClick={increment}>{count}</button>;
}
```

- A: `1`
- B: `3`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The **updater form** receives the latest queued state: `0 → 1 → 2 → 3`. This is the reliable way to compute from previous state.

</details>

---

###### 3. What's the output?

```jsx
const [state, setState] = useState({ a: 1, b: 2 });

setState({ a: 2 });
console.log(state);
```

- A: `{ a: 2, b: 2 }`
- B: `{ a: 2 }` — `b` is lost
- C: `{ a: 1, b: 2 }`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useState` **replaces** state (unlike class `this.setState` which merges). Spread old state: `setState(prev => ({ ...prev, a: 2 }))`.

</details>

---

###### 4. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    console.log('render effect', n);
  });

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

- A: Logs only on mount
- B: Logs after **every** render (no deps array)
- C: Never logs
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Omitting the deps array means the effect runs after **every** render. Useful for syncing with non-React systems; dangerous if it triggers state updates.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    console.log('run');
  }, [n, n]);

  return <button onClick={() => setN(n + 1)}>+</button>;
}
```

- A: React throws a dependency array size error
- B: Works, logs on each `n` change
- C: Logs twice per change
- D: Never logs

<details><summary><b>Answer</b></summary>

Answer: **B** (with warning in dev)

Duplicate deps entries don't cause double execution — but ESLint warns about inconsistent array size. Deps must be a stable, complete list.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const value = useMemo(() => computeExpensive(n), [n]);
  return <button onClick={() => setN(n + 1)}>{value}</button>;
}
```

When does `computeExpensive` re-run?

- A: On every render
- B: Only when `n` changes
- C: Only on mount
- D: Never

<details><summary><b>Answer</b></summary>

Answer: **B**

`useMemo` caches the result and recomputes only when deps change. It's for **expensive** calculations — trivial ones don't need it.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const cb = useCallback(() => setN(n + 1), [n]);
  return <MemoChild onClick={cb} />;
}
```

Is `cb` stable across re-renders when `n` is unchanged?

- A: No, new function every render
- B: Yes — `useCallback` returns cached reference while deps unchanged
- C: Only in production
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useCallback(fn, deps)` returns the same function reference until deps change. That makes `React.memo` children skip re-renders.

</details>

---

###### 8. What's the output?

```jsx
function App() {
  const ref = useRef(0);

  function handleClick() {
    ref.current += 1;
    console.log(ref.current);
  }

  return <button onClick={handleClick}>{ref.current}</button>;
}
```

After 3 clicks, what does the button show vs console?

- A: Button `3`, console `3`
- B: Button `0`, console `3` (no re-render)
- C: `Error`
- D: Button `3`, console `0`

<details><summary><b>Answer</b></summary>

Answer: **B**

Mutating `ref.current` does **not** trigger a re-render. Use refs for: DOM access, timers, previous values, and anything that shouldn't affect render.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const prev = useRef(n);

  useEffect(() => {
    prev.current = n;
  }, [n]);

  return <p>Now {n}, before {prev.current}</p>;
}
```

After clicking to make `n = 1`, what renders?

- A: `Now 1, before 1`
- B: `Now 1, before 0`
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

The ref is updated in an effect **after** render, so during that render it still holds the previous value `0`. This is the standard "previous value" pattern.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const ref = useRef(null);

  useEffect(() => {
    ref.current.focus();
  }, []);

  return <input ref={ref} />;
}
```

When does `.focus()` run?

- A: During render
- B: After the DOM node is mounted (effects run post-paint)
- C: Before mount
- D: Never

<details><summary><b>Answer</b></summary>

Answer: **B**

Effects run after mount — `ref.current` is set by then. Never touch refs during render (they may be `null` on first pass).

</details>

---

###### 11. What's the output?

```jsx
const ThemeContext = createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Child />
    </ThemeContext.Provider>
  );
}

function Child() {
  const theme = useContext(ThemeContext);
  return <p>{theme}</p>;
}
```

- A: `dark`
- B: `light`
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

`useContext` reads the **nearest provider above**. `Child` gets `'dark'` from `App`'s provider.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Child />
    </ThemeContext.Provider>
  );
}
```

Does `Child` re-render when `theme` changes?

- A: No — context is static
- B: Yes — new `value` object each render, consumers re-render
- C: Only with `useMemo`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

A new object literal `{ theme, setTheme }` is a **new reference** every render → all consumers re-render. Memoize the value if expensive: `useMemo(() => ({ theme, setTheme }), [theme])`.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    setN(1);
  }, []);

  useEffect(() => {
    setN(2);
  }, []);
```

- A: Final `n = 1`
- B: Final `n = 2` (last effect wins)
- C: Infinite loop
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Both mount effects run in order (top → bottom). Each sets state; last write wins → `2`. Effects don't re-run, so no loop.

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    let ignore = false;
    fetch('/api/users')
      .then(r => r.json())
      .then(d => { if (!ignore) setUsers(d); });
    return () => { ignore = true; };
  }, []);
```

Why the `ignore` flag?

- A: Performance optimization
- B: Prevents setting state after unmount / on stale responses
- C: Required by React
- D: Debugging aid

<details><summary><b>Answer</b></summary>

Answer: **B**

If the component unmounts or deps change before the promise resolves, `setState` would warn / apply stale data. The cleanup flag cancels the update. (Or use `AbortController`.)

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  if (n > 5) {
    return <p>Big</p>;
  }

  useEffect(() => {
    console.log('effect');
  }, []);

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

- A: Works, logs once on mount
- B: `Error` — hooks called after conditional return
- C: Never logs
- D: Logs every render

<details><summary><b>Answer</b></summary>

Answer: **B**

The **Rules of Hooks**: never call hooks conditionally, after early returns, or in loops. The hook order changes between renders → React throws `Rendered fewer hooks than expected`.

</details>

---

###### 16. What's the output?

```jsx
function useCounter(initial) {
  const [count, setCount] = useState(initial);
  return { count, increment: () => setCount(c => c + 1) };
}

function App() {
  const { count, increment } = useCounter(0);
  return <button onClick={increment}>{count}</button>;
}
```

Is this a valid custom hook?

- A: No — custom hooks must return JSX
- B: Yes — hooks are just functions that call other hooks
- C: Only if named `useXxx`
- D: Only with `useCallback`

<details><summary><b>Answer</b></summary>

Answer: **B**

Custom hooks are **ordinary functions** following the `use` naming convention that compose built-in hooks. They can return anything — state, objects, callbacks.

</details>

---

###### 17. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const memoized = useMemo(() => {
    return { value: n * 2 };
  }, [n]);

  return <button onClick={() => setN(n + 1)}>{memoized.value}</button>;
}
```

What does `useMemo` return?

- A: The function `() => ({ value: n * 2 })`
- B: The **result** of the function — `{ value: n * 2 }`
- C: `undefined`
- D: A promise

<details><summary><b>Answer</b></summary>

Answer: **B**

`useMemo(() => value, deps)` returns the **computed value**, not the callback. Contrast with `useCallback`, which returns the **function** itself.

</details>

---

###### 18. What's the output?

```jsx
function App() {
  const cb = useCallback(() => console.log('hi'), []);
  const memo = useMemo(() => () => console.log('hi'), []);
  console.log(cb === memo);
}
```

- A: `true` — both hold equivalent functions
- B: `false` — different cached references
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useCallback(fn, deps)` ≡ `useMemo(() => fn, deps)` conceptually, but they cache **different** calls — the returned function identities are not guaranteed equal. `cb === memo` is `false`.

</details>

---

###### 19. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <p>{count}</p>;
}
```

- A: Ticks up every second correctly
- B: Stuck at `0`
- C: Speeds up every render
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

Updater `c => c + 1` avoids stale closure, `[]` deps keep a single interval, cleanup clears it. The canonical correct timer pattern.

</details>

---

###### 20. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const ref = useRef(n);

  console.log(ref.current === n);

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

- A: Always `true`
- B: `true` on first render, then `false` (ref not auto-synced)
- C: Always `false`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useRef(n)` only sets `.current` on **first mount**. It never updates automatically — sync it yourself in an effect if you need current-value tracking.

</details>

---

###### 21. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const incrementTwice = () => {
    setN(n + 1);
    setN(n + 1);
  };

  return <button onClick={incrementTwice}>{n}</button>;
}
```

- A: `2`
- B: `1`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Both use stale `n`. Classic interview follow-up: "how do you get `2`?" → use two updater calls.

</details>

---

###### 22. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const double = n * 2;
  return <button onClick={() => setN(n + 1)}>{double}</button>;
}
```

Should you replace `const double = n * 2` with `useMemo`?

- A: Always — memoize everything
- B: No — trivial computation; only memoize expensive work
- C: Yes, or it won't update
- D: Only in production

<details><summary><b>Answer</b></summary>

Answer: **B**

`useMemo` has its own overhead. Use it for **costly** computations (heavy loops, sorting large arrays, object identity for child props) — not `n * 2`.

</details>

---

###### 23. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    const handler = () => setN(n + 1);
    window.addEventListener('keydown', handler);
    return () => window.removeEventListener('keydown', handler);
  }, [n]);
```

What happens as `n` changes?

- A: One listener forever
- B: Listener removed and re-added on every `n` change
- C: `Error`
- D: Memory leak

<details><summary><b>Answer</b></summary>

Answer: **B**

Deps `[n]` → cleanup runs, then effect re-subscribes each time. Correct but noisy. Better: use the updater `setN(c => c + 1)` with `[]` deps.

</details>

---

###### 24. What's the output?

```jsx
function App() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetchUser().then(u => {
      setUser(u);
      setLoading(false);
    });
  }, []);

  if (loading) return <Spinner />;
  return <Profile user={user} />;
}
```

Why is `loading` in state instead of derived?

- A: It must be — loading is always state
- B: Because it changes independently of other state during async work
- C: For performance
- D: It can't be derived

<details><summary><b>Answer</b></summary>

Answer: **B**

`loading` flips during async operations independently — it's genuine state. If it were purely computed from other values (like `double = n * 2`), derive it instead.

</details>

---

###### 25. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  function handleClick() {
    setTimeout(() => {
      console.log(n);
    }, 1000);
  }

  return <button onClick={handleClick}>{n}</button>;
```

You click when `n = 0`, then click again when `n = 1`. What does the first timeout log?

- A: `1`
- B: `0` — closure captured initial `n`
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Each render creates a **new closure**. The timeout from the first render captured `n = 0`. Fix: `setN(c => c + 1)` or store `n` in a ref.

</details>

---

###### 26. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const handleClick = useCallback(() => {
    setN(n + 1);
  }, [n]);

  return <button onClick={handleClick}>{n}</button>;
}
```

Is `handleClick` stable?

- A: Yes, `useCallback` always returns stable refs
- B: No — deps include `n`, so new ref each change
- C: Only in dev
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useCallback` caches until deps change. With `[n]`, every increment produces a new function. For a truly stable ref use `setN(c => c + 1)` with `[]` deps.

</details>

---

###### 27. What's the output?

```jsx
function App() {
  const ref = useRef({ count: 0 });

  function increment() {
    ref.current.count++;
    console.log(ref.current.count);
  }

  return <button onClick={increment}>{ref.current.count}</button>;
}
```

- A: Button updates each click
- B: Console updates, button stays `0` (no re-render)
- C: `Error`
- D: Both update

<details><summary><b>Answer</b></summary>

Answer: **B**

Mutating a ref (even a ref object) never re-renders. Refs are for values that **don't affect** rendered output.

</details>

---

###### 28. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const prevN = useRef();

  useEffect(() => {
    prevN.current = n;
  }, [n]);

  if (n > 100) return <p>Huge</p>;

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

- A: `Error` — effect after early return
- B: Works — hooks run before any conditional return
- C: `Error` — ref used before assignment
- D: Infinite loop

<details><summary><b>Answer</b></summary>

Answer: **B**

The hooks (`useState`, `useRef`, `useEffect`) are called at the **top**, before the `if` early return. Hook order stays consistent across renders — this is valid.

</details>

---

###### 29. What's the output?

```jsx
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handler = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handler);
    return () => window.removeEventListener('resize', handler);
  }, []);

  return width;
}

function App() {
  const width = useWindowWidth();
  return <p>{width}</p>;
}
```

- A: Updates on window resize
- B: Stuck at initial width
- C: `Error` — custom hooks can't use effects
- D: Memory leak

<details><summary><b>Answer</b></summary>

Answer: **A**

Custom hooks can use `useState` + `useEffect`. The listener syncs external (browser) state into React state and cleans up on unmount. Textbook custom hook.

</details>

---

###### 30. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  useEffect(() => {
    setN(n + 1);
  }, [n]);
```

- A: Runs once
- B: Infinite loop — effect sets `n`, deps change, effect re-runs
- C: Runs twice
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`n` changes each run → deps differ → effect runs again → sets `n` again → forever. Classic infinite-update bug. Only set state in an effect when **conditionally** or based on external systems.

</details>

---

**Next:** [05 — Rendering & Performance →](./05-Rendering-and-Performance.md)
