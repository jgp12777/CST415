# Pandemic Pathfinder

This repository contains a single-page browser game (`pandemic_pathfinder.html`) that simulates an outbreak spreading through a small city's districts. Players deploy policies to manipulate a Markov transition matrix and steer the population away from catastrophic outcomes. The game is an academic project built for CST‑415 (Project 4).

---

## 🧩 Program Overview

The entire app lives in the HTML file. It is split conceptually into several layers:

1. **Constants & configurations** –
   - `STATES`, `DISTRICTS`, thresholds, and base transition matrix (`BASE_T`).
   - Policy and event definitions include delta functions that mutate matrices and metadata (cost/duration).

2. **Game state (`G`)** –
   - Holds week counter, tokens, district data (vector `v` + transition matrix `T`), active policies/events, history and scoring information.
   - Initialized by `initGame()` which also seeds one random district with an outbreak.

3. **Markov mathematics** –
   - `stepMarkov(v,T)` performs a single week update: `v' = v × T`.
   - `steadyState(T)` obtains the long‑run distribution via power iteration.
   - Utility helpers: `deepCopy`, `clampT`, and `effectiveT` (which composes base matrices with policies/events).

4. **Game logic** –
   - `advanceWeek()` handles the weekly cycle: rolling random events, applying transitions, aging out interventions, scoring, token awards, win/loss checks.
   - `deployPolicy()` and `endGame()` manage policy deployment and end‑game UI.

5. **Hint & scoring engines** –
   - `calcBestMove()` simulates every valid policy×district pair over a 5‑week horizon and recommends the action that minimizes a weighted "danger" heuristic.
   - `scoreWeek()` compares the player's actions to the hint and issues points/bonus tokens.

6. **Rendering** –
   - A collection of `render*` functions update the DOM: top bar, map, matrix table, population bars, policy list, forecast bars, event card, logs, and buttons.
   - DOM access is centralized with a small helper `getEl()` to improve readability.

7. **Event hookups** –
   - Listeners are added programmatically (no inline `onclick` attributes) for deploy/next/hint‑toggle/restart buttons.

8. **Start** –
   - `initGame()` is called at the end of the script once the DOM is ready (script is placed at bottom of `<body>`).

---

## ✅ Improvements & Fixes

The code review identified a few opportunities:

- **Strict mode** (`'use strict';`) added to catch common JavaScript mistakes.
- Removed inline event handlers for better separation of concerns and accessibility.
- Added keyboard focus and `keypress` handlers for district tiles; improved focus style in CSS.
- Introduced a `getEl()` helper to reduce repeated DOM lookups.
- Minor code hygiene: some `let` declarations converted to `const` where mutation wasn’t required.
- Added ARIA‑friendly labels and contrast‑aware styles to improve accessibility.
- Scoped global state inside a single object (`G`) and avoided polluting the global namespace.

These changes align with industry best practices such as:

- **Separation of concerns** – logic vs. presentation.
- **Immutable bindings** – prefer `const`, minimize `var`/`let` where feasible.
- **Accessibility** – semantic HTML, keyboard support, focus indicators, ARIA roles where appropriate.
- **Performance** – caching DOM references, avoiding unnecessary reflows.
- **Maintainability** – descriptive variable names, modular functions, and extensive comments.

---

## 📁 File Structure

```
CST415Project4/
└── pandemic_pathfinder.html   # single HTML/JS/CSS game
└── README.md                 # overview & notes (this file)
```

---

## 🛠 How to Run

Open `pandemic_pathfinder.html` in a modern desktop browser (Chrome, Firefox, Edge). No build tools or servers required.

---

## 📚 Further Notes

- The underlying model uses discrete-time Markov chains with a 5‑state compartmental model (S, I, H, R, D).
- Policies manipulate transition probabilities; random events temporarily perturb them.
- The hint engine is a simple brute‑force lookahead and can be extended with caching or more sophisticated heuristics.
- Scores are accumulated and displayed on an end‑game screen along with a chart drawn on a `<canvas>`.

Feel free to refactor into modules or port to a framework if extending the project. The code is licensed under the MIT/academic use license (implicit).

Enjoy playing, experimenting, and learning!