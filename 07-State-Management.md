# 07 — State Management (MCQ) 🟡🔴

> Context, Redux Toolkit, Zustand, and choosing the right tool.

---

###### 1. What's the output?

```jsx
const CountContext = createContext(0);

function App() {
  return (
    <CountContext.Provider value={42}>
      <Display />
    </CountContext.Provider>
  );
}

function Display() {
  const count = useContext(CountContext);
  return <p>{count}</p>;
}
```

- A: `0`
- B: `42`
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useContext` reads the nearest provider's value above the component. The default `0` applies only when there's **no provider**.

</details>

---

###### 2. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  return (
    <CountContext.Provider value={n}>
      <Display />
      <button onClick={() => setN(n + 1)}>+</button>
    </CountContext.Provider>
  );
}
```

When `n` changes, does `Display` re-render?

- A: No — context is static
- B: Yes — new provider value → consumers re-render
- C: Only if `Display` uses `useState`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Context consumers re-render whenever the provider's `value` prop changes.

</details>

---

###### 3. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const value = { n, setN };

  return (
    <CountContext.Provider value={value}>
      <Display />
      <button onClick={() => setN(n + 1)}>+</button>
    </CountContext.Provider>
  );
}
```

Does `value` break memoization for consumers?

- A: No — objects are stable
- B: Yes — new object literal every render → all consumers re-render
- C: Only in dev
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`{ n, setN }` is a **new reference** each render → memoized consumers still re-render. Fix: `useMemo(() => ({ n, setN }), [n])`, or split contexts.

</details>

---

###### 4. What's the output?

```jsx
// Redux Toolkit slice
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment(state) {
      state.value += 1; // mutating?
    },
  },
});

dispatch(counterSlice.actions.increment());
```

Why is `state.value += 1` OK here?

- A: It isn't — Redux disallows it
- B: Immer lets you "mutate" drafts; RTK produces a new immutable state
- C: Redux uses classes
- D: Only works in dev

<details><summary><b>Answer</b></summary>

Answer: **B**

Redux Toolkit wraps reducers with **Immer**. You write mutating-looking code; Immer returns a new immutable state under the hood.

</details>

---

###### 5. What's the output?

```jsx
const state = { user: { name: 'A' } };

// plain Redux (no RTK) — bad reducer
function reducer(state = { user: { name: 'A' } }, action) {
  if (action.type === 'rename') {
    state.user.name = 'B'; // direct mutation
    return state;
  }
  return state;
}
```

What happens after dispatch?

- A: State updates in the UI
- B: Nothing — same reference, subscribers not notified (mutation bug)
- C: `Error`
- D: App crashes

<details><summary><b>Answer</b></summary>

Answer: **B**

Redux/React state relies on **new references**. Mutating and returning the same object means `===` equal → no re-render. Never mutate — use spread or Immer.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const count = useSelector((state) => state.counter.value);
  return <p>{count}</p>;
}
```

If `state.counter.value` doesn't change but `state.user` does:

- A: `App` re-renders
- B: `App` skips re-render (selector result unchanged)
- C: `Error`
- D: Page reloads

<details><summary><b>Answer</b></summary>

Answer: **B**

`useSelector` re-runs the selector and **shallow-compares** the result — unchanged output → no re-render.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const items = useSelector((state) => state.items);
  return <List items={items} />;
}
```

If the selector returns `state.items` (an array) and the reducer returns a **new array** with same contents:

- A: Skips re-render (shallow equal elements?)
- B: Re-renders — new array reference fails shallow compare of the top level
- C: `Error`
- D: Freezes

<details><summary><b>Answer</b></summary>

Answer: **B**

`useSelector` default `===` / shallow check compares the **selected value itself**. A new array reference ≠ old reference → re-render. Return primitive slices or use `shallowEqual`.

</details>

---

###### 8. What's the output?

```jsx
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

function Counter() {
  const count = useStore((s) => s.count);
  return <button onClick={() => useStore.getState().increment()}>{count}</button>;
}
```

Does `Counter` re-render when `count` changes?

- A: No
- B: Yes — selector returned new `count`
- C: Only with `useContext`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Zustand (like Redux) re-renders only components whose **selected slice changed**. Works outside React via `getState()` / `setState()`.

</details>

---

###### 9. What's the output?

```jsx
const useStore = create((set) => ({ n: 0 }));

function App() {
  const n = useStore((s) => s.n);
  const inc = useStore((s) => s.inc);
  return <button onClick={inc}>{n}</button>;
}
```

- A: Works — selector returns stable function
- B: Could re-render unnecessarily if `inc` isn't defined with `useCallback`-like stability
- C: `Error`
- D: Zustand requires Context

<details><summary><b>Answer</b></summary>

Answer: **B**

Zustand actions defined once in the store are stable — fine here. The lesson: selector results must be **stable references** to avoid extra renders.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [user, setUser] = useState(null);
  return (
    <AuthContext.Provider value={{ user, setUser }}>
      <Dashboard />
    </AuthContext.Provider>
  );
}
```

When `user` changes, which components re-render?

- A: Only `App`
- B: `App` and every consumer of `AuthContext`
- C: Every component in the app
- D: None

<details><summary><b>Answer</b></summary>

Answer: **B**

Provider value change → all `useContext(AuthContext)` consumers re-render. Non-consumers are unaffected.

</details>

---

###### 11. What's the output?

```jsx
// Before Redux Toolkit
const store = createStore(reducer, applyMiddleware(thunk));

// With RTK
const store = configureStore({ reducer });
```

What does `configureStore` add by default?

- A: Nothing
- B: Redux DevTools + thunk middleware + immutability checks
- C: React binding
- D: Router

<details><summary><b>Answer</b></summary>

Answer: **B**

`configureStore` sets up sane defaults: `redux-thunk`, serializability & immutability checks (dev), DevTools — no manual `applyMiddleware` needed.

</details>

---

###### 12. What's the output?

```jsx
const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: (state) => state + 1,
  },
});

dispatch({ type: 'counter/increment' });
dispatch(counterSlice.actions.increment());
```

Are these two dispatches the same action?

- A: No
- B: Yes — the generated action type is `counter/increment`
- C: Only if payload matches
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Slice action types are auto-namespaced: `sliceName/reducerName`. `increment()` generates `{ type: 'counter/increment' }`.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  function fetchUsers() {
    return (dispatch) => {
      dispatch({ type: 'users/loading' });
      fetch('/api/users')
        .then(r => r.json())
        .then(data => dispatch({ type: 'users/success', payload: data }));
    };
  }
}
```

Why return a function from the action creator?

- A: It's required for all actions
- B: It's a **thunk** — async logic runs, dispatching plain actions on success/failure
- C: Redux can't call APIs
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

With `redux-thunk` (RTK default), action creators can return functions for async work — dispatching loading/success/error plain actions.

</details>

---

###### 14. What's the output?

```jsx
const useCounterStore = create((set) => ({
  count: 0,
  inc: () => set((s) => ({ count: s.count + 1 })),
}));

function App() {
  const count = useCounterStore((s) => s.count);
  return <Child count={count} />;
}
```

Does `Child` re-render when `inc` is called but `count` stays the same?

- A: Yes
- B: No — selector output unchanged
- C: `Error`
- D: Only in dev

<details><summary><b>Answer</b></summary>

Answer: **B**

Zustand only re-renders when the **selected value** changes. Same value → no re-render.

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const [state, dispatch] = useReducer(reducer, 0);

  function reducer(state, action) {
    switch (action.type) {
      case 'inc': return state + 1;
      default: return state;
    }
  }

  return <button onClick={() => dispatch({ type: 'inc' })}>{state}</button>;
}
```

Is `useReducer` better than `useState` here?

- A: Always better
- B: Equivalent — `useState` is simpler for one value; `useReducer` shines for complex state logic
- C: No — `useReducer` is deprecated
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useReducer` centralizes update logic (great for multiple sub-values or complex transitions). For a single counter, `useState` is fine.

</details>

---

###### 16. What's the output?

```jsx
function App() {
  const [state, dispatch] = useReducer(reducer, { a: 0, b: 0 });

  function reducer(state, action) {
    switch (action.type) {
      case 'incA':
        return { ...state, a: state.a + 1 };
      default:
        return state;
    }
  }
```

Why return a **new object** in the reducer?

- A: Style preference
- B: React compares references — a new object triggers re-render; mutation may be ignored
- C: Reducers must be async
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Reducers must be **pure** and return new references on change. Mutating the same object breaks change detection.

</details>

---

###### 17. What's the output?

```jsx
// Context + useReducer for "global" state
function App() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <StateContext.Provider value={{ state, dispatch }}>
      <Routes />
    </StateContext.Provider>
  );
}
```

When is this sufficient instead of Redux/Zustand?

- A: Always — Context replaces Redux everywhere
- B: For small/medium apps with few updates; every consumer re-renders on any value change
- C: Only for styling
- D: Never

<details><summary><b>Answer</b></summary>

Answer: **B**

Context has **no selector optimization** — all consumers re-render on any provider value change. Fine for auth/theme; heavy global state benefits from Redux/Zustand selectors.

</details>

---

###### 18. What's the output?

```jsx
const useStore = create(persist(
  (set) => ({ n: 0, inc: () => set((s) => ({ n: s.n + 1 })) }),
  { name: 'my-store' }
));
```

What does `persist` middleware do?

- A: Caches in memory only
- B: Persists store to `localStorage` (or custom storage) and hydrates on load
- C: Saves to server
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`persist` writes the store to storage (default `localStorage`) and rehydrates it on startup — common Zustand pattern.

</details>

---

###### 19. What's the output?

```jsx
dispatch({ type: 'counter/increment' });
dispatch({ type: 'counter/increment' });
```

In Redux, do dispatches run synchronously?

- A: Async — batched by Redux
- B: Synchronous — reducer runs immediately for each dispatch
- C: Depends on middleware
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Redux dispatches are **synchronous** — the reducer runs right away and subscribers are notified (middleware like thunk can defer **future** dispatches).

</details>

---

###### 20. What's the output?

```jsx
// React 19 — use()
const dataPromise = fetchData();

function App() {
  const data = use(dataPromise);
  return <p>{data}</p>;
}
```

What does `use()` do here?

- A: Fetches automatically
- B: Reads the **resolved** value of the promise (suspends until ready)
- C: Creates state
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

React 19's `use()` unwraps promises/resources and **suspends** the component until resolved (with `<Suspense>`). It can also read context.

</details>

---

**Next:** [08 — Forms & Events →](./08-Forms-and-Events.md)
