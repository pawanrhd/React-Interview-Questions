# 11 — React 18 & 19 Features (MCQ) 🔴

> Concurrent rendering, Transitions, Suspense, Server Components, and React 19 — heavily asked in 2026.

---

###### 1. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  function handle() {
    setN(1);
    fetch('/a').then(() => setN(2));
    setTimeout(() => setN(3), 0);
  }

  return <button onClick={handle}>{n}</button>;
}
```

In React 18+, how many re-renders after click?

- A: 3
- B: 1 (auto-batching across all sources)
- C: 2
- D: 0

<details><summary><b>Answer</b></summary>

Answer: **B**

React 18 **auto-batches** updates from events, promises, and timeouts into a single render. (React 17 batched only inside React events.)

</details>

---

###### 2. What's the output?

```jsx
import { createRoot } from 'react-dom/client';

createRoot(document.getElementById('root')).render(<App />);
```

What's required to opt **into** React 18?

- A: `ReactDOM.render`
- B: Using `createRoot` from `react-dom/client`
- C: Installing React 17
- D: Nothing — automatic

<details><summary><b>Answer</b></summary>

Answer: **B**

Legacy `ReactDOM.render` runs in React 17 compatibility mode. `createRoot` enables the concurrent root + all React 18 features.

</details>

---

###### 3. What's the output?

```jsx
function App() {
  const [query, setQuery] = useState('');

  function handleChange(e) {
    startTransition(() => {
      setQuery(e.target.value);
      setFiltered(filterHugeList(e.target.value));
    });
  }
}
```

What does `startTransition` do?

- A: Debounces
- B: Marks updates as **non-urgent** — React can interrupt them to keep input responsive
- C: Makes updates synchronous
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Transitions keep urgent input (typing) snappy while expensive list updates render **interruptibly** — the core of concurrent rendering.

</details>

---

###### 4. What's the output?

```jsx
function App() {
  const [isPending, startTransition] = useTransition();

  return (
    <button disabled={isPending} onClick={() => startTransition(() => heavy())}>
      {isPending ? 'Loading...' : 'Search'}
    </button>
  );
}
```

During the transition, what's `isPending`?

- A: Always `false`
- B: `true` while the transition is in progress
- C: `null`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useTransition` returns `[isPending, startTransition]` — use it for spinners/disable buttons while deferred work runs.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  const deferredQuery = useDeferredValue(query);

  return <ResultList search={deferredQuery} />;
}
```

If `query` changes rapidly:

- A: `deferredQuery` lags behind (stale) while urgent UI updates immediately
- B: They're always identical
- C: `Error`
- D: Query is dropped

<details><summary><b>Answer</b></summary>

Answer: **A**

`useDeferredValue` returns a **lagging** copy — the input stays responsive while the heavy list catches up. No manual `startTransition` needed.

</details>

---

###### 6. What's the output?

```jsx
<Suspense fallback={<Spinner />}>
  <Profile />
</Suspense>
```

When does `<Spinner />` show?

- A: Always
- B: When a child suspends (e.g. reads a promise via `use`/lazy) before ready
- C: Only on error
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`Suspense` catches **suspension** — lazy components, `use(promise)`, data frameworks — and shows `fallback` until resolved.

</details>

---

###### 7. What's the output?

```jsx
const LazyChart = React.lazy(() => import('./Chart'));

function App() {
  return (
    <Suspense fallback={<p>Loading chart...</p>}>
      <LazyChart />
    </Suspense>
  );
}
```

- A: Chart code downloads on first render → spinner shown meanwhile
- B: Chart bundled from the start
- C: `Error`
- D: Nothing renders

<details><summary><b>Answer</b></summary>

Answer: **A**

`React.lazy` **code-splits** — the chunk loads on demand while `Suspense` shows the fallback. Defers initial bundle size.

</details>

---

###### 8. What's the output?

```jsx
// React 19
async function UserProfile({ userId }) {
  const user = await fetchUser(userId); // Server Component
  return <p>{user.name}</p>;
}
```

Where does this component run (in Next.js App Router)?

- A: Always in the browser
- B: On the **server** — ships only the rendered HTML, not the function
- C: In a web worker
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

**React Server Components** (framework-provided, e.g. Next.js) render on the server — heavy deps and data fetching stay off the client bundle.

</details>

---

###### 9. What's the output?

```jsx
'use client';

import { useState } from 'react';

export function Counter() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

What does `'use client'` do?

- A: Makes the component fetch from client APIs
- B: Marks the boundary — this module and its imports run in the browser
- C: Enables localStorage
- D: Required for all components

<details><summary><b>Answer</b></summary>

Answer: **B**

`'use client'` defines the **server/client boundary** — everything imported by it becomes client code. Server Components are the default in RSC setups.

</details>

---

###### 10. What's the output?

```jsx
// React 19
function App() {
  const [name, setName] = useState('Ada');

  return (
    <form action={async (formData) => {
      'use server';
      await saveName(formData.get('name'));
    }}>
      <input name="name" defaultValue={name} />
      <button>Save</button>
    </form>
  );
}
```

What's a Server Action?

- A: A server function callable from the client without an API route
- B: A Redux action
- C: A service worker
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

React 19 Server Actions (`'use server'`) let the client invoke **async server functions** directly — form mutations without hand-written API endpoints.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  const ref = useRef();

  useEffect(() => {
    console.log(ref.current);
  }, []);

  return <input ref={ref} />;
}
```

In React 19, can you **ref cleanup** like a function?

- A: No
- B: Yes — refs can return a cleanup function from the ref callback: `ref={(el) => { ...; return () => ...; }}`
- C: `Error`
- D: Only class refs

<details><summary><b>Answer</b></summary>

Answer: **B**

React 19 added **ref cleanup functions** in ref callbacks (like effects), fixing leaks from manually wiring `addEventListener` in refs.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const [msg, setMsg] = useState('');

  return (
    <>
      <input
        value={msg}
        onChange={(e) => setMsg(e.target.value)}
        placeholder="Message"
      />
      <button type="button">Send</button>
    </>
  );
}
```

In React 19, does `<button>` default to `type="submit"` **outside** forms?

- A: Yes — browser behavior, even outside forms
- B: No behavioral issue; buttons outside forms don't submit anyway
- C: `Error`
- D: React changes the default

<details><summary><b>Answer</b></summary>

Answer: **B**

HTML default `type="submit"` only matters **inside** forms. Best practice (and common interview answer): always set `type="button"` for non-submit buttons to avoid accidental form submits.

</details>

---

###### 13. What's the output?

```jsx
import { use, Suspense } from 'react';

const userPromise = fetchUser();

function App() {
  const user = use(userPromise);
  return <p>{user.name}</p>;
}
```

- A: `Error` — `use` isn't a hook
- B: Reads the promise, suspends until resolved
- C: Returns the promise object
- D: Requires `useEffect`

<details><summary><b>Answer</b></summary>

Answer: **B**

React 19's `use()` reads promises/resources in render and **suspends** (needs `<Suspense>`). Unlike `useEffect`, it works with conditional logic.

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [text, setText] = useState('');
  const deferred = useDeferredValue(text);

  return (
    <>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <HeavyMemo value={deferred} />
    </>
  );
}
```

Which is urgent — `text` or `deferred`?

- A: Both deferred
- B: `text` urgent; `deferred` may be stale then catch up
- C: `deferred` urgent
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Input (`text`) updates immediately. `HeavyMemo` gets `deferred` — possibly the previous value — then re-renders when idle. Typing stays smooth.

</details>

---

###### 15. What's the output?

```jsx
<StrictMode>
  <App />
</StrictMode>
```

Which features are StrictMode-enhanced in development?

- A: Double-invoking renders/effects, checking effect cleanup, purity checks
- B: Enables production batching
- C: Adds routing
- D: Speeds up builds

<details><summary><b>Answer</b></summary>

Answer: **A**

StrictMode is **dev-only**: double-render, double-effect + cleanup, warns about impure renders and missing cleanups. Zero cost/behavior change in production.

</details>

---

**Next:** [12 — Coding Challenges →](./12-Coding-Challenges.md)
