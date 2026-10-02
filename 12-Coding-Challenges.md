# 12 — Coding Challenges (MCQ) 🔴

> The live-coding tasks and follow-ups interviewers ask while you write React.

---

###### 1. What's the output?

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

Interview follow-up: make the button show `+3` per click. Correct fix?

- A: Call `setCount(count + 1)` three times
- B: Call `setCount(c => c + 1)` three times
- C: Use `useRef` instead
- D: Wrap in `setTimeout`

<details><summary><b>Answer</b></summary>

Answer: **B**

Updaters chain on the latest state: `0 → 1 → 2 → 3`. Direct values all compute from stale `count`.

</details>

---

###### 2. What's the output?

```jsx
function DebouncedSearch() {
  const [query, setQuery] = useState('');

  useEffect(() => {
    const id = setTimeout(() => {
      search(query);
    }, 500);
    return () => clearTimeout(id);
  }, [query]);
}
```

Type `react` — how many `search` calls?

- A: 5 (one per keystroke)
- B: 1 — timeout resets each keystroke
- C: 0
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Cleanup clears the pending timeout on every `query` change; only the final keystroke's timer survives → single call after 500ms.

</details>

---

###### 3. What's the output?

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);

  function toggle(id) {
    setTodos(todos.map(t =>
      t.id === id ? { ...t, done: !t.done } : t
    ));
  }
}
```

Why map instead of mutate?

- A: Style
- B: Map returns a **new array** with a **new object** for the changed item — React sees references changed
- C: `map` is async
- D: Required for keys

<details><summary><b>Answer</b></summary>

Answer: **B**

Immutable update: new array + new changed object → change detection works. Mutating `t.done` in place would be invisible (or buggily shared).

</details>

---

###### 4. What's the output?

```jsx
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li>{item.name}</li>
      ))}
    </ul>
  );
}
```

What's the console warning about?

- A: Syntax error
- B: Missing `key` prop on list items
- C: `map` is invalid
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Each child in a list needs a stable unique `key` → `<li key={item.id}>`. Without it React warns and diffing degrades.

</details>

---

###### 5. What's the output?

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Child onUpdate={() => setCount(count + 1)} />
      <p>{count}</p>
    </div>
  );
}

const Child = React.memo(({ onUpdate }) => {
  console.log('child');
  return <button onClick={onUpdate}>+</button>;
});
```

Does `child` log when `count` changes?

- A: No — memo blocks it
- B: Yes — inline arrow is new each render, memo fails
- C: Only in dev
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

New function identity → shallow compare fails → child re-renders. Fix: `useCallback(() => setCount(c => c + 1), [])` in `Parent`.

</details>

---

###### 6. What's the output?

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then(r => r.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

What's the cleanup missing?

- A: Nothing
- B: Abort / ignore flag — prevent state updates after unmount or URL change
- C: `Error`
- D: `setInterval`

<details><summary><b>Answer</b></summary>

Answer: **B**

Fast URL switches or unmount → stale responses set state. Add `AbortController` (or `let ignore = false`) in cleanup.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const [show, setShow] = useState(true);

  return (
    <>
      {show && <Expensive />}
      <button onClick={() => setShow(!show)}>Toggle</button>
    </>
  );
}
```

When `show` flips to `false`, `Expensive`:

- A: Stays mounted with hidden CSS
- B: Unmounts — state destroyed, cleanup runs
- C: `Error`
- D: Pauses

<details><summary><b>Answer</b></summary>

Answer: **B**

`&&` conditional rendering **removes** the subtree — unmount effects/cleanup run, state discarded. Re-mount starts fresh. (CSS hiding preserves state.)

</details>

---

###### 8. What's the output?

```jsx
function Modal({ children, onClose }) {
  return createPortal(
    <div className="overlay" onClick={onClose}>
      <div className="content" onClick={(e) => e.stopPropagation()}>
        {children}
      </div>
    </div>,
    document.body
  );
}
```

Why `stopPropagation` on the content?

- A: Performance
- B: Prevents overlay close when clicking **inside** the modal
- C: Required by React
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Clicks bubble — without `stopPropagation`, clicking anywhere inside would hit the overlay's `onClick` and close the modal.

</details>

---

###### 9. What's the output?

```jsx
function App() {
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

After 3 seconds?

- A: `3`
- B: `1` — stale closure
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The interval closed over `count = 0` once → always `0 + 1 = 1`. Fix: `setCount(c => c + 1)`.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  function logN() {
    console.log(n);
  }

  useEffect(() => {
    window.addEventListener('click', logN);
    return () => window.removeEventListener('click', logN);
  }, []);

  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

After clicking the button twice, clicking anywhere logs:

- A: `2`
- B: `0` — effect captured initial closure
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`[]` deps → listener registered once with the first render's `logN` closure (`n = 0`). Fix: sync `n` into a ref, or re-subscribe with `[n]`, or use a functional read.

</details>

---

###### 11. What's the output?

```jsx
function Stopwatch() {
  const [time, setTime] = useState(0);
  const [running, setRunning] = useState(false);

  useEffect(() => {
    if (!running) return;
    const id = setInterval(() => setTime(t => t + 1), 100);
    return () => clearInterval(id);
  }, [running]);

  return <p>{time}</p>;
}
```

Toggle `running` off then on. What happens to `time`?

- A: Resets to 0
- B: Continues from current value — interval only starts/stops
- C: Doubles
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`time` state persists; the effect only manages the interval lifecycle based on `running`. Cleanup stops ticking without clearing state.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [items, setItems] = useState([]);

  function add() {
    setItems([...items, items.length + 1]);
  }

  function addFive() {
    for (let i = 0; i < 5; i++) add();
  }

  return <button onClick={addFive}>{items.length}</button>;
}
```

After clicking, what's `items.length`?

- A: 6
- B: 1
- C: 5
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`add()` reads stale `items` (always `[]`) each call → each sets `[1]`. Fix: `setItems(prev => [...prev, prev.length + 1])` or compute once.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const update = () => setN(prev => prev + 1);

  return (
    <>
      <button onClick={() => { update(); update(); }}>{n}</button>
    </>
  );
}
```

- A: `1`
- B: `2`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Two updater calls → `0 → 1 → 2`. This is what interviewers want when they say "make both clicks count."

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [text, setText] = useState('');

  const onChange = (e) => {
    setText(e.target.value);
  };

  return (
    <Child text={text} onChange={onChange} />
  );
}

const Child = React.memo(({ text, onChange }) => {
  console.log('child');
  return <input value={text} onChange={onChange} />;
});
```

Type one character. Does `child` log?

- A: No — memo always skips
- B: Yes — `text` changed (correctly re-renders); `onChange` stays stable
- C: `Error`
- D: Never logs

<details><summary><b>Answer</b></summary>

Answer: **B**

`onChange` is a stable top-level function; `text` changes each keystroke → memo correctly re-renders. Expected behavior.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const ref = useRef();

  function save() {
    localStorage.setItem('draft', ref.current.value);
  }

  return (
    <>
      <textarea ref={ref} />
      <button onClick={save}>Save</button>
    </>
  );
}
```

Why `ref` instead of state here?

- A: Refs are faster to type
- B: Value doesn't affect rendered output — no re-renders needed; pure imperative access
- C: State can't hold text
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Refs are for values that **don't drive the UI**. Controlled state is for values that appear in render or drive other logic. This is the classic "state vs ref" interview question.

</details>

---

**End of collection.** Back to [README](./README.md) for the full table of contents.
