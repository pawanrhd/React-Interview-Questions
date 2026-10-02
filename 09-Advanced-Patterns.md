# 09 — Advanced Patterns (MCQ) 🔴

> HOCs, render props, compound components, and design patterns.

---

###### 1. What's the output?

```jsx
function withLogger(WrappedComponent) {
  return function Enhanced(props) {
    console.log('render', WrappedComponent.name);
    return <WrappedComponent {...props} />;
  };
}

const LoggedButton = withLogger(Button);
```

What pattern is this?

- A: Higher-Order Component (HOC)
- B: Custom hook
- C: Mixin
- D: Decorator

<details><summary><b>Answer</b></summary>

Answer: **A**

A **HOC** is a function that takes a component and returns a new component with added behavior — wraps and enhances via props.

</details>

---

###### 2. What's the output?

```jsx
function withTheme(Wrapped) {
  return function Enhanced(props) {
    const theme = useContext(ThemeContext);
    return <Wrapped {...props} theme={theme} />;
  };
}

const ThemedCard = withTheme(Card);
```

What does `ThemedCard` receive?

- A: Only `props` passed to it
- B: Passed props **plus** injected `theme` prop
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

HOCs **inject props** — `{...props}` passes user props through, `theme={theme}` adds the enhanced prop.

</details>

---

###### 3. What's the output?

```jsx
function MouseTracker({ render }) {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const move = (e) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', move);
    return () => window.removeEventListener('mousemove', move);
  }, []);

  return render(pos);
}

<MouseTracker render={(pos) => <p>{pos.x}, {pos.y}</p>} />;
```

What pattern is this?

- A: HOC
- B: Render prop
- C: Custom hook
- D: Context

<details><summary><b>Answer</b></summary>

Answer: **B**

A **render prop** passes a function as a prop — the child decides what to render with the shared state/behavior.

</details>

---

###### 4. What's the output?

```jsx
function useMouse() {
  const [pos, setPos] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const move = (e) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', move);
    return () => window.removeEventListener('mousemove', move);
  }, []);

  return pos;
}
```

This refactors the render-prop component into:

- A: A HOC
- B: A custom hook
- C: Context
- D: A class

<details><summary><b>Answer</b></summary>

Answer: **B**

Custom hooks are the modern replacement for many render props / HOCs — reusable logic without nesting.

</details>

---

###### 5. What's the output?

```jsx
function Tab({ children }) {
  const [active, setActive] = useState(0);
  return (
    <TabContext.Provider value={{ active, setActive }}>
      {children}
    </TabContext.Provider>
  );
}

<Tab>
  <TabList>
    <TabButton index={0}>One</TabButton>
    <TabButton index={1}>Two</TabButton>
  </TabList>
  <TabPanels>
    <TabPanel index={0}>First</TabPanel>
  </TabPanels>
</Tab>
```

What pattern is this?

- A: HOC
- B: Compound component
- C: Render prop
- D: Redux

<details><summary><b>Answer</b></summary>

Answer: **B**

**Compound components** share implicit state (via context) while composing like `<Tab>` + children — flexible, reusable APIs (Tabs, Select, Accordion).

</details>

---

###### 6. What's the output?

```jsx
const ExpandedContext = createContext();

function Expander({ children }) {
  const [open, setOpen] = useState(false);
  return (
    <ExpandedContext.Provider value={{ open, toggle: () => setOpen(o => !o) }}>
      <div>{children}</div>
    </ExpandedContext.Provider>
  );
}

function Header() {
  const { toggle } = useContext(ExpandedContext);
  return <button onClick={toggle}>Toggle</button>;
}
```

- A: Works — context shares state across compound children
- B: `Error`
- C: Only `Header` renders
- D: State lost on toggle

<details><summary><b>Answer</b></summary>

Answer: **A**

Context is the glue for compound components — children access shared state without prop drilling.

</details>

---

###### 7. What's the output?

```jsx
function ControlledInput({ value, onChange }) {
  return <input value={value} onChange={(e) => onChange(e.target.value)} />;
}

function Form() {
  const [name, setName] = useState('');
  return <ControlledInput value={name} onChange={setName} />;
}
```

This follows the pattern of:

- A: Uncontrolled component
- B: Controlled component (state lifted to parent)
- C: HOC
- D: Ref forwarding

<details><summary><b>Answer</b></summary>

Answer: **B**

State lives in `Form`, child is fully controlled by props — the standard controlled pattern.

</details>

---

###### 8. What's the output?

```jsx
function Input(props, ref) {
  return <input ref={ref} {...props} />;
}

const FancyInput = forwardRef(Input);

function App() {
  const ref = useRef();
  return <FancyInput ref={ref} placeholder="type" />;
}
```

- A: `Error` — function components can't take refs
- B: Works — `forwardRef` exposes the inner DOM node
- C: `ref` is ignored
- D: Only class components support refs

<details><summary><b>Answer</b></summary>

Answer: **B**

`forwardRef` lets a component receive `ref` and pass it down to a DOM node / child. (React 19 makes `ref` a normal prop — no `forwardRef` needed.)

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [open, setOpen] = useState(false);

  return (
    <>
      <button onClick={() => setOpen(o => !o)}>Toggle</button>
      {open && <Modal onClose={() => setOpen(false)} />}
    </>
  );
}
```

Why `setOpen(o => !o)` instead of `setOpen(!open)`?

- A: It's faster
- B: Functional update uses latest state — safe even if multiple toggles queue
- C: Required syntax
- D: Avoids errors

<details><summary><b>Answer</b></summary>

Answer: **B**

`!open` could be stale in batched updates; `o => !o` always operates on the **latest** state.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [show, setShow] = useState(false);

  return (
    <div>
      <button onClick={() => setShow(true)}>Open</button>
      {show && <Modal />}
    </div>
  );
}

function Modal() {
  useEffect(() => {
    const handler = (e) => {
      if (e.key === 'Escape') setShow(false);
    };
    window.addEventListener('keydown', handler);
    return () => window.removeEventListener('keydown', handler);
  }, []);
  return <div>Modal</div>;
}
```

Why put the listener in `Modal` instead of `App`?

- A: It's not — better in `App`
- B: Colocation — logic lives with the component that owns it, cleans up on unmount
- C: `Error`
- D: Performance only

<details><summary><b>Answer</b></summary>

Answer: **B**

**Colocating** related state/effect with the component that uses it reduces prop noise and guarantees cleanup when `Modal` unmounts.

</details>

---

###### 11. What's the output?

```jsx
function App() {
  const [count, setCount] = useState(0);

  function incrementThree() {
    setCount(c => c + 1);
    setCount(c => c + 1);
    setCount(c => c + 1);
  }

  return <button onClick={incrementThree}>{count}</button>;
}
```

- A: `1`
- B: `3`
- C: `2`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Chained updaters each receive the latest queued value: `0 → 1 → 2 → 3`.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const ref = useRef();

  useEffect(() => {
    const observer = new MutationObserver(() => console.log('changed'));
    observer.observe(ref.current, { childList: true });
    return () => observer.disconnect();
  }, []);

  return <div ref={ref} />;
}
```

- A: Observes DOM mutations, disconnects on unmount
- B: `Error`
- C: Memory leak
- D: Never logs

<details><summary><b>Answer</b></summary>

Answer: **A**

Refs + effects = bridge to **non-React** APIs (observers, canvas, third-party libs). Cleanup prevents leaks.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const double = useMemo(() => n * 2, [n]);

  return <ExpensiveView value={double} />;
}

const ExpensiveView = React.memo(({ value }) => {
  console.log('expensive');
  return <p>{value}</p>;
});
```

When `n` changes, does `expensive` log?

- A: No — memo blocks everything
- B: Yes — `double` changed → memo props differ → re-render
- C: Only on mount
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Memoization composes: `useMemo` produces new `double` → `React.memo` sees changed `value` → re-renders (correctly — value changed).

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);
  const stable = useMemo(() => ({ n }), [n]);

  return <Child obj={stable} />;
}

const Child = React.memo(({ obj }) => <p>{obj.n}</p>);
```

If `n` unchanged across parent re-renders, does `Child` re-render?

- A: Yes
- B: No — `stable` reference unchanged, memo skips
- C: `Error`
- D: Only in dev

<details><summary><b>Answer</b></summary>

Answer: **B**

`useMemo` preserves object **identity** → `React.memo` shallow compare passes → skip.

</details>

---

###### 15. What's the output?

```jsx
function useDebounce(value, delay) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);

  return debounced;
}

function Search() {
  const [q, setQ] = useState('');
  const debouncedQ = useDebounce(q, 300);
  useEffect(() => {
    if (debouncedQ) fetchResults(debouncedQ);
  }, [debouncedQ]);
}
```

Type `react` quickly. How many searches fire?

- A: One per keystroke (5)
- B: One — debounced value updates once after pause
- C: Zero
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

The timeout resets on each keystroke; only the final `react` (after 300ms idle) updates `debouncedQ` → single fetch. Classic debouncing.

</details>

---

###### 16. What's the output?

```jsx
function App() {
  const [open, setOpen] = useState(false);
  const ref = useRef();

  useEffect(() => {
    function onClick(e) {
      if (ref.current && !ref.current.contains(e.target)) {
        setOpen(false);
      }
    }
    document.addEventListener('mousedown', onClick);
    return () => document.removeEventListener('mousedown', onClick);
  }, []);

  return <div ref={ref}>Dropdown {open ? 'open' : 'closed'}</div>;
}
```

What does clicking **outside** the div do?

- A: Nothing
- B: Closes the dropdown
- C: `Error`
- D: Opens it

<details><summary><b>Answer</b></summary>

Answer: **B**

`contains()` checks ancestry — clicks outside fail the check → `setOpen(false)`. The **click-outside** pattern for dropdowns/modals.

</details>

---

###### 17. What's the output?

```jsx
function App() {
  const [portal, setPortal] = useState(false);

  return (
    <>
      <button onClick={() => setPortal(true)}>Show</button>
      {portal && createPortal(<Overlay />, document.body)}
    </>
  );
}
```

Where does `<Overlay />` render in the DOM?

- A: Inside the parent div
- B: As a child of `document.body` (bypasses overflow/z-index stacking)
- C: In a new window
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`createPortal` renders React children into a **different DOM node** — for modals, tooltips, toasts that must escape `overflow: hidden`.

</details>

---

###### 18. What's the output?

```jsx
const ErrorBoundary = React.Component;

class Boundary extends ErrorBoundary {
  state = { error: null };

  static getDerivedStateFromError(error) {
    return { error };
  }

  render() {
    if (this.state.error) return <p>Something went wrong</p>;
    return this.props.children;
  }
}

<Boundary>
  <Buggy />
</Boundary>
```

- A: Catches render errors in children, shows fallback
- B: Catches async promise errors
- C: Catches event handler errors
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

Error boundaries catch errors **during render/lifecycle** — show fallback UI. They do NOT catch errors in event handlers, async code, or `setTimeout` (wrap those manually).

</details>

---

###### 19. What's the output?

```jsx
function App() {
  const [theme, setTheme] = useState('light');

  const value = useMemo(() => ({ theme, setTheme }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      <Child />
    </ThemeContext.Provider>
  );
}
```

Why `useMemo` for `value`?

- A: Required by React
- B: Prevents new object identity each render → avoids re-rendering all consumers
- C: Speeds up fetch
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

New object each render → all consumers re-render even if `theme` unchanged. Memoizing keeps identity stable while deps are unchanged.

</details>

---

###### 20. What's the output?

```jsx
function App() {
  const [n, setN] = useState(0);

  const inc = useCallback(() => setN(c => c + 1), []);
  const dec = useCallback(() => setN(c => c - 1), []);

  return <Buttons onInc={inc} onDec={dec} />;
}
```

Are `inc`/`dec` stable across renders?

- A: No — new each render
- B: Yes — empty deps → same references forever
- C: Only in prod
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Empty `[]` deps → `useCallback` returns cached functions. Combined with `React.memo` children, this **skips** re-renders.

</details>

---

**Next:** [10 — Testing →](./10-Testing.md)
