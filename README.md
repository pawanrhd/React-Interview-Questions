# ⚛️ React Interview Questions — MCQ Edition

> **265+ hand-crafted questions** in the addictive "What's the output?" style made famous by Lydia Hallie.  
> No fluff. No filler. Just the exact patterns interviewers actually ask in 2026.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![React](https://img.shields.io/badge/React-18%2B-61dafb)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)
![Questions](https://img.shields.io/badge/Questions-265%2B-orange)
![Difficulty](https://img.shields.io/badge/Level-Basic%20%7C%20Intermediate%20%7C%20Advanced-purple)

---

## 🎯 Why this repo?

| You're a... | You'll get... |
|-------------|---------------|
| 🧑‍💻 **Fresher** | Crystal-clear fundamentals — no more "I think it's..." |
| 👨‍💻 **Mid-level** | The gotchas that separate juniors from seniors |
| 🏆 **Senior** | Internals, patterns, and React 18/19 features you *know* but can't explain |
| 🎓 **Anyone** | A structured path from "what is useState" to "how does concurrent rendering actually work" |

---

## 📚 The Complete Syllabus

| # | Topic | Questions | What You'll Master |
|---|-------|-----------|-------------------|
| 01 | [React Fundamentals](./01-React-Fundamentals.md) | 60 | JSX, elements, reconciliation, keys, controlled inputs |
| 02 | [Components & Props](./02-Components-and-Props.md) | 20 | Composition, children, prop drilling, memo pitfalls |
| 03 | [State & Lifecycle](./03-State-and-Lifecycle.md) | 20 | useState quirks, useEffect timing, cleanup, batching |
| 04 | [Hooks Deep Dive](./04-Hooks-Deep-Dive.md) | 30 | useMemo, useCallback, useRef, custom hooks, rules |
| 05 | [Rendering & Performance](./05-Rendering-and-Performance.md) | 20 | Transitions, deferred values, memoization strategies |
| 06 | [React Router](./06-React-Router.md) | 15 | Nested routes, loaders, actions, search params |
| 07 | [State Management](./07-State-Management.md) | 20 | Context, Redux Toolkit, Zustand, server state |
| 08 | [Forms & Events](./08-Forms-and-Events.md) | 15 | Controlled/uncontrolled, validation, synthetic events |
| 09 | [Advanced Patterns](./09-Advanced-Patterns.md) | 20 | HOCs, render props, compound components, portals |
| 10 | [Testing](./10-Testing.md) | 15 | RTL queries, async testing, mocking, userEvent |
| 11 | [React 18 & 19](./11-React-18-and-19.md) | 15 | Concurrent features, Server Components, Actions, `use()` |
| 12 | [Coding Challenges](./12-Coding-Challenges.md) | 15 | Live-coding tasks with follow-up questions |

**Total: 265+ MCQs** — every single one with a hidden answer + deep-dive explanation.

---

## 🧠 How to Actually Learn from This

```
1️⃣  READ the code — don't skim. Trace it line by line.
2️⃣  PREDICT the output — say it out loud or write it down.
3️⃣  CLICK "Answer" — were you right? If not, *why*?
4️⃣  INTERNALIZE the explanation — that's the gold.
5️⃣  REPEAT tomorrow. Spaced repetition > cramming.
```

> **Pro tip**: The questions are ordered by dependency. Start at 01. Jump around only if you're reviewing.

---

## 🔥 Sample Question (this is the format)

<details>
<summary><b>👀 Click to see the format</b></summary>

```jsx
function App() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

**After one click, what does the button show?**

- A: `3`
- B: `1`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

**Answer: B**

All three `setCount(count + 1)` calls capture the **stale** `count = 0` from the current render. Each computes `0 + 1 = 1`. React batches them → final state is `1`.

✅ **Fix**: `setCount(c => c + 1)` three times → `3`.

</details>
</details>

---

## 🏷️ Difficulty Legend

- 🟢 **Basic** — definitions, syntax, "what does this do?"
- 🟡 **Intermediate** — gotchas, comparisons, practical usage
- 🔴 **Advanced** — internals, performance, architecture, React 19

---

## 🚀 Quick Start (Zero Config)

```bash
# Just read — no install needed
open 01-React-Fundamentals.md
```

```bash
# Or run the interactive quiz app
git clone https://github.com/pawan/React-Interview-Questions.git
cd React-Interview-Questions
npm install && npm run dev
# → http://localhost:5173
```

---

## 🤝 Contribute

Found a bug? Missing a topic? Want harder questions?

1. Fork → branch → PR
2. Follow the existing MCQ format
3. Add explanation — that's where the learning lives

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

---

## 📄 License

MIT — use it, share it, build on it.

---

## ⭐ If this helped you land the job...

Star the repo. Share it with your study group. Pay it forward.

<p align="center">
  <strong>Made with ❤️ for React developers who want to stop guessing and start knowing.</strong>
</p>

---

<p align="center">
  <a href="./01-React-Fundamentals.md">▶️ Start with Fundamentals</a> •
  <a href="#-the-complete-syllabus">📋 View All Topics</a> •
  <a href="https://github.com/pawan/React-Interview-Questions">🔗 GitHub</a>
</p>