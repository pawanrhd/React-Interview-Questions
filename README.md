# ⚛️ React Interview Questions — MCQ Format

A collection of **React JS interview questions in MCQ (multiple-choice) format**, inspired by the popular "What's the output?" style of Lydia Hallie's JavaScript questions.

Each question shows a code snippet and asks **"What's the output?"** (or a concept question), followed by 4 options. Click the **Answer** dropdown to reveal the explanation.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![React](https://img.shields.io/badge/React-18+-61dafb)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)

---

## 📚 Table of Contents

| # | Topic | Questions |
|---|-------|-----------|
| 01 | [React Fundamentals](./01-React-Fundamentals.md) | 60 |
| 02 | [Components & Props](./02-Components-and-Props.md) | 20 |
| 03 | [State & Lifecycle](./03-State-and-Lifecycle.md) | 20 |
| 04 | [Hooks Deep Dive](./04-Hooks-Deep-Dive.md) | 30 |
| 05 | [Rendering & Performance](./05-Rendering-and-Performance.md) | 20 |
| 06 | [React Router](./06-React-Router.md) | 15 |
| 07 | [State Management (Redux, Zustand, Context)](./07-State-Management.md) | 20 |
| 08 | [Forms & Events](./08-Forms-and-Events.md) | 15 |
| 09 | [Advanced Patterns](./09-Advanced-Patterns.md) | 20 |
| 10 | [Testing](./10-Testing.md) | 15 |
| 11 | [React 18 & 19 Features](./11-React-18-and-19.md) | 15 |
| 12 | [Coding Challenges](./12-Coding-Challenges.md) | 15 |

**Total: 265+ MCQs** — each with a hidden answer + explanation.

---

## 🎯 Who is this for?

- 🧑💻 **Freshers** preparing for their first React job
- 👨💻 **Mid-level developers** targeting product companies
- 🏆 **Senior engineers** brushing up on internals & patterns
- 🎓 Anyone who wants a **structured React revision**

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- npm or yarn

### Run the interactive quiz (optional)

```bash
git clone https://github.com/pawan/React-Interview-Questions.git
cd React-Interview-Questions
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) to practice with the interactive quiz app.

### Just want to read?

Every topic is a standalone Markdown file — start with [01-React-Fundamentals.md](./01-React-Fundamentals.md) or jump to any topic above.

---

## 📖 How to use this repo

1. **Read topic-wise** — each file builds on the previous one.
2. **Guess the output** — read the code snippet and pick an option *before* revealing the answer.
3. **Reveal the answer** — click the `<details>` block to check yourself and read the explanation.
4. **Practice the challenges** — `12-Coding-Challenges.md` has live coding tasks asked in real interviews.
5. **Quiz yourself** — the bundled Vite app randomly quizzes you from `src/data/questions.js`.
6. **Star ⭐ the repo** if it helps you!

---

## 🗂️ Project Structure

```
React-Interview-Questions/
├── 01-React-Fundamentals.md
├── 02-Components-and-Props.md
├── 03-State-and-Lifecycle.md
├── 04-Hooks-Deep-Dive.md
├── 05-Rendering-and-Performance.md
├── 06-React-Router.md
├── 07-State-Management.md
├── 08-Forms-and-Events.md
├── 09-Advanced-Patterns.md
├── 10-Testing.md
├── 11-React-18-and-19.md
├── 12-Coding-Challenges.md
├── quiz-app/               # Interactive quiz (Vite + React)
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── App.css
│       └── data/questions.js
├── .github/
│   └── workflows/ci.yml    # GitHub Actions CI
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

---

## 🧠 Difficulty Levels

Questions are tagged by difficulty:

- 🟢 **Basic** — definitions, concepts, simple examples
- 🟡 **Intermediate** — practical usage, gotchas, comparisons
- 🔴 **Advanced** — internals, performance, architecture, patterns

---

## ✍️ MCQ Format Example

Every question follows this pattern:

````
###### 1. What's the output?

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

- A: `3`
- B: `1`
- C: `0`
- D: `Error`

<details><summary><b>Answer</b></summary>

Answer: **B**

`setCount(count + 1)` uses the **stale** `count` value from the current render
(`0`) in all three calls. React batches them, and each computes `0 + 1 = 1`.
Use the updater form `setCount(c => c + 1)` to get `3`.

</details>
````

---

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](./CONTRIBUTING.md) first.

1. Fork the repo
2. Create your branch: `git checkout -b feature/new-questions`
3. Commit: `git commit -m "Add questions on X"`
4. Push: `git push origin feature/new-questions`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the [MIT License](./LICENSE).

---

## ⭐ Show your support

If this repo helped you, please give it a ⭐ — it helps others discover it!

<p align="center">Made with ❤️ for the React community</p>
