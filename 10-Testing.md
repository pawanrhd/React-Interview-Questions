# 10 — Testing (MCQ) 🟡

> React Testing Library, Jest/Vitest, and query strategy questions.

---

###### 1. What's the output?

```jsx
render(<Button>Click me</Button>);

const btn = screen.getByRole('button', { name: /click me/i });
fireEvent.click(btn);
```

Why query by **role**?

- A: It's the fastest query
- B: It matches how users/assistive tech perceive the UI — accessible and resilient
- C: Required by Jest
- D: Only works for buttons

<details><summary><b>Answer</b></summary>

Answer: **B**

Testing Library's guidance: query by **accessible role/name** first — tests survive class/id/CSS changes.

</details>

---

###### 2. What's the output?

```jsx
render(<Input />);

screen.getByText('Email');
```

What's wrong if the label is in a `<label>`?

- A: Nothing
- B: Should use `getByLabelText('Email')` for form fields
- C: `Error` — `getByText` doesn't exist
- D: Use `getByClassName`

<details><summary><b>Answer</b></summary>

Answer: **B**

Form fields → `getByLabelText`. Text → `getByText`. Buttons/links → `getByRole`.

</details>

---

###### 3. What's the output?

```jsx
render(<App />);

const el = screen.getByText('Loading');
```

If `Loading` disappears after fetch:

- A: Test fails immediately if not present at query time
- B: `getBy*` throws if not found — use `findBy*` (async) or `queryBy*` (null allowed)
- C: Waits automatically
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`getBy` = throw if missing. `queryBy` = `null` if missing (for asserting absence). `findBy` = async wait (returns promise, uses `waitFor` internally).

</details>

---

###### 4. What's the output?

```jsx
test('submits', async () => {
  render(<Form />);
  fireEvent.change(screen.getByLabelText(/name/i), {
    target: { value: 'Ada' },
  });
  fireEvent.click(screen.getByRole('button', { name: /submit/i }));
  expect(await screen.findByText(/thanks ada/i)).toBeInTheDocument();
});
```

Why `findByText`?

- A: `getByText` is deprecated
- B: It waits for the async update (fetch/mock resolution) before asserting
- C: It's faster
- D: Required for `fireEvent`

<details><summary><b>Answer</b></summary>

Answer: **B**

Post-click state updates may be async — `findBy*` polls until found or timeout (wraps `waitFor`).

</details>

---

###### 5. What's the output?

```jsx
test('counter', () => {
  render(<Counter />);
  fireEvent.click(screen.getByRole('button'));
  expect(screen.getByText('1')).toBeInTheDocument();
});
```

What library is this?

- A: Enzyme
- B: React Testing Library (with Jest/Vitest)
- C: Cypress
- D: Mocha

<details><summary><b>Answer</b></summary>

Answer: **B**

RTL (`@testing-library/react`) — tests **behavior via the DOM**, not component internals.

</details>

---

###### 6. What's the output?

```jsx
test('shows error', async () => {
  render(<Login />);
  fireEvent.click(screen.getByRole('button'));
  expect(await screen.findByRole('alert')).toHaveTextContent('Invalid');
});
```

What does `toHaveTextContent` come from?

- A: Jest core
- B: `@testing-library/jest-dom` matchers
- C: React
- D: ESLint

<details><summary><b>Answer</b></summary>

Answer: **B**

`jest-dom` adds DOM-specific matchers: `toBeInTheDocument`, `toHaveTextContent`, `toBeVisible`, etc.

</details>

---

###### 7. What's the output?

```jsx
jest.mock('./api', () => ({
  fetchUser: jest.fn().mockResolvedValue({ name: 'Ada' }),
}));

test('loads user', async () => {
  render(<Profile />);
  expect(await screen.findByText('Ada')).toBeInTheDocument();
});
```

What does `jest.mock` do?

- A: Deletes the module
- B: Replaces the module with a mock — no real network call
- C: Spies without replacing
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`jest.mock` **replaces** the module factory. `jest.spyOn` only wraps (calls through). Mocking keeps tests fast and deterministic.

</details>

---

###### 8. What's the output?

```jsx
test('calls onClick', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Go</Button>);
  fireEvent.click(screen.getByRole('button'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

- A: Verifies the callback fired once
- B: `Error`
- C: Checks return value
- D: Mocks the DOM

<details><summary><b>Answer</b></summary>

Answer: **A**

`jest.fn()` creates a spy — assert **calls** and **args** (`toHaveBeenCalledWith`).

</details>

---

###### 9. What's the output?

```jsx
test('effect cleanup', () => {
  const addSpy = jest.spyOn(window, 'addEventListener');
  const removeSpy = jest.spyOn(window, 'removeEventListener');

  const { unmount } = render(<Comp />);
  unmount();

  expect(removeSpy).toHaveBeenCalled();
});
```

Why test cleanup?

- A: It's not testable
- B: Preventing leaks is a key `useEffect` contract — cleanup should run on unmount
- C: Required by CI
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

Assertions on cleanup catch missing `removeEventListener` / `clearInterval` — a common React bug.

</details>

---

###### 10. What's the output?

```jsx
test('renders', async () => {
  render(<App />);
  expect(screen.getByText('Home')).toBeInTheDocument();
});
```

Where does `screen` come from?

- A: Must be destructured from `render`
- B: Auto-imported from RTL — queries the whole `document.body`
- C: `jest`
- D: React

<details><summary><b>Answer</b></summary>

Answer: **B**

`screen` is RTL's default query surface on `document.body` — no need to capture `render`'s container.

</details>

---

###### 11. What's the output?

```jsx
test('async button', async () => {
  render(<AsyncButton />);
  await userEvent.click(screen.getByRole('button'));
  expect(await screen.findByText('Done')).toBeInTheDocument();
});
```

Why `userEvent.click` over `fireEvent.click`?

- A: It's identical
- B: `userEvent` simulates the full user interaction sequence (pointer, focus, key events) — closer to real browsers
- C: It's async only
- D: `fireEvent` is removed

<details><summary><b>Answer</b></summary>

Answer: **B**

`@testing-library/user-event` handles focus, hover, and key sequences — preferred for realistic tests. `fireEvent` dispatches a single low-level event.

</details>

---

###### 12. What's the output?

```jsx
test('snapshot', () => {
  const { container } = render(<Card title="Hi" />);
  expect(container).toMatchSnapshot();
});
```

What does `toMatchSnapshot` do?

- A: Compares to a stored snapshot file (created on first run)
- B: Compares pixels
- C: Always passes
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

Snapshots save rendered output to `__snapshots__`. Regenerate intentionally with `jest -u`. Use sparingly — they're brittle for style changes.

</details>

---

###### 13. What's the output?

```jsx
test('form validation', () => {
  render(<Signup />);
  fireEvent.click(screen.getByRole('button', { name: /sign up/i }));
  expect(screen.getByRole('alert')).toHaveTextContent(/required/i);
});
```

What's being tested?

- A: Network layer
- B: UI behavior — validation message appears on empty submit
- C: CSS
- D: Database

<details><summary><b>Answer</b></summary>

Answer: **B**

RTL tests **observable behavior** — what the user sees — not implementation details (state variables, internals).

</details>

---

###### 14. What's the output?

```jsx
test('theme toggle', () => {
  render(<App />);
  const initial = document.body.className;
  fireEvent.click(screen.getByRole('button', { name: /toggle theme/i }));
  expect(document.body.className).not.toBe(initial);
});
```

- A: Valid — asserts user-visible DOM change
- B: `Error` — never touch `document` directly
- C: Flaky always
- D: Requires Cypress

<details><summary><b>Answer</b></summary>

Answer: **A**

Asserting on real DOM changes (classes, attributes) is fine — it's the output users experience.

</details>

---

###### 15. What's the output?

```jsx
// Arrange
render(<Todo />);
// Act
fireEvent.change(screen.getByLabelText(/add todo/i), {
  target: { value: 'Buy milk' },
});
fireEvent.click(screen.getByRole('button', { name: /add/i }));
// Assert
expect(screen.getByText('Buy milk')).toBeInTheDocument();
```

What pattern is this?

- A: AAA (Arrange–Act–Assert)
- B: Singleton
- C: Observer
- D: Factory

<details><summary><b>Answer</b></summary>

Answer: **A**

**Arrange–Act–Assert** — the standard test structure, mirrored by RTL's `render` → interact → query.

</details>

---

**Next:** [11 — React 18 & 19 Features →](./11-React-18-and-19.md)
