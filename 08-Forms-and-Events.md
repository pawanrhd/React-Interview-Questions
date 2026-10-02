# 08 — Forms & Events (MCQ) 🟢🟡

> Controlled vs uncontrolled inputs, synthetic events, and form gotchas.

---

###### 1. What's the output?

```jsx
function App() {
  const [text, setText] = useState('');

  return <input value={text} onChange={(e) => setText(e.target.value)} />;
}
```

Type `hi`. What does `text` become?

- A: `h` then `hi`
- B: `hi` only
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

Controlled input: each keystroke fires `onChange` → `setText` → re-render with new value.

</details>

---

###### 2. What's the output?

```jsx
function App() {
  return <input value="fixed" onChange={(e) => console.log(e.target.value)} />;
}
```

User types. What happens?

- A: Value updates + logs
- B: Value stuck on `fixed` — controlled input with no state update (warning)
- C: `Error`
- D: Input cleared

<details><summary><b>Answer</b></summary>

Answer: **B**

`value` without updating state = read-only input. React warns: *You provided a `value` prop to a form field without an `onChange` handler.*

</details>

---

###### 3. What's the output?

```jsx
function App() {
  return <input defaultValue="hi" />;
}
```

Type more characters. What's the value?

- A: Stays `hi`
- B: Updates freely (uncontrolled — DOM owns value)
- C: `Error`
- D: Input disabled

<details><summary><b>Answer</b></summary>

Answer: **B**

`defaultValue` sets the **initial** value only; after that the DOM controls it.

</details>

---

###### 4. What's the output?

```jsx
function App() {
  const ref = useRef();

  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      console.log(ref.current.value);
    }}>
      <input ref={ref} />
      <button>Send</button>
    </form>
  );
}
```

- A: Logs input value, no page reload
- B: Page reloads
- C: `Error`
- D: Nothing logs

<details><summary><b>Answer</b></summary>

Answer: **A**

`e.preventDefault()` stops the native form submit reload; `ref.current.value` reads the DOM value.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  return (
    <form onSubmit={(e) => console.log('sent')}>
      <button>Send</button>
    </form>
  );
}
```

- A: Logs `sent`, no reload
- B: Logs `sent` AND reloads the page
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

Form submit without `preventDefault()` triggers the browser's default navigation → full reload. Always call `e.preventDefault()`.

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const [text, setText] = useState('');

  return (
    <input
      value={text}
      onChange={(e) => setText(e.target.value.toUpperCase())}
    />
  );
}
```

Type `abc`. What shows?

- A: `abc`
- B: `ABC`
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

Transform in the handler → state holds `ABC` → input renders `ABC`. Data derived from events.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  return (
    <select onChange={(e) => console.log(e.target.value)} defaultValue="b">
      <option value="a">A</option>
      <option value="b">B</option>
    </select>
  );
}
```

- A: Uncontrolled select, initial `b`, logs on change
- B: Controlled — must update state
- C: `Error`
- D: Nothing selected

<details><summary><b>Answer</b></summary>

Answer: **A**

Same rules as inputs: `defaultValue` = uncontrolled. `e.target.value` is the selected option's value (**string**).

</details>

---

###### 8. What's the output?

```jsx
function App() {
  const [checked, setChecked] = useState(false);

  return (
    <input
      type="checkbox"
      checked={checked}
      onChange={(e) => setChecked(e.target.checked)}
    />
  );
}
```

- A: Works — `checked` + `onChange` = controlled checkbox
- B: Always checked
- C: `Error`
- D: Read-only

<details><summary><b>Answer</b></summary>

Answer: **A**

Checkboxes use `checked` (not `value`) + `onChange`. `e.target.checked` is a **boolean**.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  const [text, setText] = useState('');

  return (
    <>
      <input onChange={(e) => setText(e.target.value)} />
      <p>{text}</p>
    </>
  );
}
```

Is this fully controlled?

- A: Yes
- B: No — missing `value={text}`; DOM value can drift from state
- C: `Error`
- D: Read-only

<details><summary><b>Answer</b></summary>

Answer: **B**

Controlled requires **both** `value` and `onChange`. Without `value`, it behaves as uncontrolled while still tracking state.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  const [text, setText] = useState('a');

  function clear() {
    setText('');
  }

  return (
    <>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={clear}>Clear</button>
    </>
  );
}
```

Click Clear. What shows?

- A: Input still `a`
- B: Input empties
- C: `Error`
- D: Page reloads

<details><summary><b>Answer</b></summary>

Answer: **B**

Setting state to `''` updates the controlled input. Note: `null`/`undefined` would warn (switch to `defaultValue` or use `''`).

</details>

---

###### 11. What's the output?

```jsx
function App() {
  return (
    <input
      onInput={(e) => console.log('input')}
      onChange={(e) => console.log('change')}
    />
  );
}
```

Type one character. What logs?

- A: `input` only
- B: `change` only
- C: `input` then `change`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **C**

React's synthetic `onChange` behaves like `onInput` (fires every keystroke). Native `change` on text inputs fires on blur — React normalizes it, but both can fire in React's system.

</details>

---

###### 12. What's the output?

```jsx
function App() {
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(e.target.tagName);
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Go</button>
    </form>
  );
}
```

- A: `FORM` — `e.target` is the form
- B: `BUTTON`
- C: `INPUT`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

In `onSubmit`, `e.target` is the **form** element. The element that fired the event is `e.currentTarget` in some cases — for submit, target = form.

</details>

---

###### 13. What's the output?

```jsx
function App() {
  const [val, setVal] = useState('');

  return (
    <input
      value={val}
      onChange={(e) => setVal(e.target.value)}
      onKeyDown={(e) => {
        if (e.key === 'Enter') console.log('enter');
      }}
    />
  );
}
```

Press Enter. What logs?

- A: `enter`
- B: Nothing
- C: `Error` — Enter not a key
- D: Form submits

<details><summary><b>Answer</b></summary>

Answer: **A**

`e.key` from the synthetic event identifies the key. `Enter` works with `onKeyDown`. If inside a form without `preventDefault`, it may also submit.

</details>

---

###### 14. What's the output?

```jsx
function App() {
  const [items, setItems] = useState([]);

  function addItem(e) {
    e.preventDefault();
    const value = e.target.item.value;
    setItems([...items, value]);
    e.target.reset();
  }

  return (
    <form onSubmit={addItem}>
      <input name="item" />
      <button>Add</button>
    </form>
  );
}
```

- A: Adds item via form data + resets input (uncontrolled pattern)
- B: `Error` — no state
- C: Page reloads
- D: Input never clears

<details><summary><b>Answer</b></summary>

Answer: **A**

Access form fields via `e.target.<name>` and `.reset()` — a valid **uncontrolled** form pattern (often less boilerplate than full control).

</details>

---

###### 15. What's the output?

```jsx
function App() {
  const [data, setData] = useState({ name: '', email: '' });

  function handleChange(e) {
    setData({ ...data, [e.target.name]: e.target.value });
  }

  return (
    <form>
      <input name="name" value={data.name} onChange={handleChange} />
      <input name="email" value={data.email} onChange={handleChange} />
    </form>
  );
}
```

What does the spread + computed key achieve?

- A: Updates only the changed field, keeps the rest
- B: Clears other fields
- C: `Error`
- D: Adds new keys

<details><summary><b>Answer</b></summary>

Answer: **A**

`{ ...data, [e.target.name]: e.target.value }` — one handler for all inputs; spreads old state, overwrites only the changed field. The standard multi-field form pattern.

</details>

---

**Next:** [09 — Advanced Patterns →](./09-Advanced-Patterns.md)
