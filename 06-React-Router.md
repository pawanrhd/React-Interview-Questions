# 06 — React Router (MCQ) 🟡

> Declarative routing, params, nested routes, and the data APIs asked in 2026 interviews.

---

###### 1. What's the output?

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

What renders at `/about`?

- A: `Home`
- B: `About`
- C: Both
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

`Routes` matches the current URL to the most specific `path`. `/about` → `About`.

</details>

---

###### 2. What's the output?

```jsx
<Route path="/users/:id" element={<User />} />

function User() {
  const { id } = useParams();
  return <p>{id}</p>;
}
```

At `/users/42`, what does `User` render?

- A: `42`
- B: `:id`
- C: `Error`
- D: `undefined`

<details><summary><b>Answer</b></summary>

Answer: **A**

`useParams()` parses dynamic segments — `id = '42'` (always a **string**).

</details>

---

###### 3. What's the output?

```jsx
function App() {
  return (
    <BrowserRouter>
      <Link to="/about">About</Link>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

What does clicking `Link` do?

- A: Full page reload
- B: Client-side navigation — no reload, URL updates
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

`Link` intercepts navigation and updates the URL via the History API — the SPA stays mounted.

</details>

---

###### 4. What's the output?

```jsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route path="about" element={<About />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

function Layout() {
  return (
    <div>
      <Nav />
      <Outlet />
    </div>
  );
}
```

What does `Outlet` render at `/about`?

- A: Nothing
- B: `<About />` — the matched child route
- C: `Error`
- D: `<Nav />`

<details><summary><b>Answer</b></summary>

Answer: **B**

`Outlet` is where **child routes** render inside a parent layout — the core of nested routing.

</details>

---

###### 5. What's the output?

```jsx
function App() {
  return <a href="/about">About</a>;
}
```

Compared to `<Link to="/about">`:

- A: Identical behavior
- B: `<a href>` triggers a full page reload
- C: `<a href>` doesn't work in React
- D: `<Link>` reloads, `<a>` doesn't

<details><summary><b>Answer</b></summary>

Answer: **B**

Plain `<a>` does a browser navigation (full reload — loses state). Use `<Link>` for SPA transitions. (Use `<a>` only for external URLs.)

</details>

---

###### 6. What's the output?

```jsx
function App() {
  const navigate = useNavigate();
  return <button onClick={() => navigate('/dashboard')}>Go</button>;
}
```

- A: Reloads and goes to `/dashboard`
- B: Programmatic client-side navigation
- C: `Error`
- D: Nothing

<details><summary><b>Answer</b></summary>

Answer: **B**

`useNavigate()` returns a function for **imperative** navigation — after login, form submit, redirects.

</details>

---

###### 7. What's the output?

```jsx
function App() {
  const navigate = useNavigate();
  return <button onClick={() => navigate(-1)}>Back</button>;
}
```

- A: Goes back one entry in history
- B: Goes to `/`
- C: `Error`
- D: Reloads

<details><summary><b>Answer</b></summary>

Answer: **A**

`navigate(-1)` is like `history.back()`. Positive numbers go forward (`navigate(1)`).

</details>

---

###### 8. What's the output?

```jsx
<Route path="/user/:id" element={<User />} />

function User() {
  const [searchParams, setSearchParams] = useSearchParams();
  const tab = searchParams.get('tab') || 'profile';
  return <p>{tab}</p>;
}
```

At `/user/1?tab=settings`, what is `tab`?

- A: `settings`
- B: `profile`
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **A**

`useSearchParams()` reads the query string — `tab = 'settings'`. Default applies only when the param is missing.

</details>

---

###### 9. What's the output?

```jsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

What renders at `/random-path`?

- A: `Home`
- B: `NotFound`
- C: Blank page
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`path="*"` is the **catch-all** / 404 route — matches anything not handled above.

</details>

---

###### 10. What's the output?

```jsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/old" element={<Navigate to="/new" replace />} />
        <Route path="/new" element={<New />} />
      </Routes>
    </BrowserRouter>
  );
}
```

Visiting `/old`:

- A: Shows `<New />`
- B: Redirects to `/new` without adding a history entry (`replace`)
- C: `Error`
- D: Shows `Home`

<details><summary><b>Answer</b></summary>

Answer: **B**

`Navigate` performs a redirect; `replace` swaps the history entry so Back doesn't loop to `/old`.

</details>

---

###### 11. What's the output?

```jsx
<Route path="/files" element={<Files />} loader={filesLoader} />

async function filesLoader() {
  return fetch('/api/files').then(r => r.json());
}

function Files() {
  const data = useLoaderData();
  return <List items={data} />;
}
```

What does `useLoaderData()` return?

- A: The promise
- B: The **resolved value** returned by the loader
- C: `undefined`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

React Router's data APIs (`loader` + `useLoaderData`) give you the **resolved** data — no `useEffect` fetch boilerplate.

</details>

---

###### 12. What's the output?

```jsx
<Route
  path="/submit"
  action={submitAction}
  element={<Form />}
/>

async function submitAction({ request }) {
  const fd = await request.formData();
  return redirect('/done');
}
```

How do you trigger `action`?

- A: `onClick`
- B: A `<Form method="post">` submission (declarative)
- C: Automatically on mount
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`<Form>` (from react-router) submits to the route's `action` without a full page reload. `redirect()` sends the user onward.

</details>

---

###### 13. What's the output?

```jsx
<Route
  path="/post/:id"
  element={<Post />}
  errorElement={<ErrorPage />}
/>

function Post() {
  const { id } = useParams();
  const { data } = useFetcher(); // wrong hook here
}
```

What should you use to **read loader data** in `Post`?

- A: `useFetcher()`
- B: `useLoaderData()`
- C: `useParams()`
- D: `useState`

<details><summary><b>Answer</b></summary>

Answer: **B**

`useLoaderData()` returns the route loader's data. `useFetcher()` is for triggering loads/actions **without** navigation (e.g. background revalidation).

</details>

---

###### 14. What's the output?

```jsx
function App() {
  return (
    <HashRouter>
      <Routes>
        <Route path="/home" element={<Home />} />
      </Routes>
    </HashRouter>
  );
}
```

What's the URL for `Home`?

- A: `/home`
- B: `/#/home`
- C: `/home.html`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`HashRouter` puts the route **after `#`** — useful when the server can't handle SPA fallback routes.

</details>

---

###### 15. What's the output?

```jsx
<Route path="/users" element={<Users />}>
  <Route path=":id" element={<UserDetail />} />
</Route>
```

At `/users/5`, what renders?

- A: `<Users />` only
- B: `<Users />` with `<Outlet />` filled by `<UserDetail />`
- C: `Error`
- D: `<UserDetail />` only

<details><summary><b>Answer</b></summary>

Answer: **B**

Nested routes render inside the parent's `<Outlet />`. `<UserDetail />` gets `id = '5'` from `useParams()`.

</details>

---

**Next:** [07 — State Management →](./07-State-Management.md)
