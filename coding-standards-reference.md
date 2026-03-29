# React Coding Standards Reference

Standards and grep patterns for auditing React applications.

## Hooks Rules

> **References:**
> - [React: Rules of Hooks](https://react.dev/reference/rules/rules-of-hooks)
> - [eslint-plugin-react-hooks](https://github.com/facebook/react/tree/main/packages/eslint-plugin-react-hooks)

Grep patterns:
- `if.*\buse[A-Z]` — conditional hook call
- `for.*\buse[A-Z]` or `\.forEach.*\buse[A-Z]` — hook in loop
- `\?\s*use[A-Z]` — ternary hook call
- `&&\s*use[A-Z]` — short-circuit hook call
- `try\s*\{[^}]*\buse[A-Z]` — hook inside try/catch block
- `catch\s*\([^)]*\)\s*\{[^}]*\buse[A-Z]` — hook inside catch block
- `useEffect\(\s*\(\)\s*=>.*\buse[A-Z]` — hook called inside useEffect callback
- `useMemo\(\s*\(\)\s*=>.*\buse[A-Z]` — hook called inside useMemo callback

## Naming Conventions

> **References:**
> - [React: Your First Component](https://react.dev/learn/your-first-component)
> - [Airbnb React/JSX Style Guide: Naming](https://github.com/airbnb/javascript/tree/master/react#naming)

- Component files: PascalCase (`UserProfile.jsx`)
- Hook files: camelCase with `use` prefix (`useAuth.js`)
- Utility files: camelCase (`formatDate.js`)
- Constants: SCREAMING_SNAKE_CASE (`MAX_RETRIES`)
- Boolean state: `is`/`has`/`should` prefix (`isLoading`, `hasError`)
- Event handler props: `on` prefix (`onClick`, `onItemSelect`)

Grep patterns:
- `export default function [a-z]` — component exported with lowercase name (won't work as JSX)
- `export function [a-z].*return.*<` — lowercase function returning JSX

## State Management

> **References:**
> - [React: Updating Objects in State](https://react.dev/learn/updating-objects-in-state)
> - [React: Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)
> - [React: Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure)
> - [React: Sharing State Between Components](https://react.dev/learn/sharing-state-between-components)
> - [React: Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context)

Grep patterns (array mutations — React requires immutable updates):
- `state\.push\(` or `state\.pop\(` — array push/pop mutation
- `state\.splice\(` or `state\.shift\(` or `state\.unshift\(` — array splice/shift mutation
- `state\.reverse\(` or `state\.sort\(` — in-place array mutation (use `[...arr].sort()` instead)
- `state\[.*\]\s*=` — direct array index assignment
- `state\.\w+\s*=\s*` — object property mutation after useState
- `state\.\w+\.\w+\s*=` — nested object property mutation
- `state\.\w+\+\+` or `state\.\w+--` — increment/decrement on state

Flag:
- `useEffect` that only calls `setState` with data derivable from existing state/props (should derive during render instead)
- Props passed through 3+ component levels (prop drilling)
- `useState\(props\.` — mirroring props in state (goes stale; prefix with `initial`/`default` if intentional)
- Two boolean `useState` calls representing mutually exclusive states (should be a single status enum)
- Deeply nested state objects (3+ levels) — suggest flattening/normalizing

## Key Props

> **References:**
> - [React: Rendering Lists](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)

Grep patterns:
- `key=\{.*index` or `key=\{i\}` or `key=\{idx\}` — index as key
- `\.map\(` in JSX — verify each mapped element has a `key` prop

## Effects

> **References:**
> - [React: Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
> - [React: You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
> - [React: Lifecycle of Reactive Effects](https://react.dev/learn/lifecycle-of-reactive-effects)

Grep patterns (cleanup required):
- `setInterval` inside `useEffect` — check for `clearInterval` cleanup
- `setTimeout` inside `useEffect` — check for `clearTimeout` cleanup
- `addEventListener` inside `useEffect` — check for `removeEventListener` cleanup
- `subscribe` inside `useEffect` — check for unsubscribe cleanup
- `fetch\(` inside `useEffect` — check for abort/ignore pattern (race condition risk)

Flag ("You Might Not Need an Effect" anti-patterns):
- Effects that only transform props into state (derive during render or use `useMemo`)
- Effects that reset state on prop change (use `key` to reset component)
- Effects that filter/map/reduce data into state (use `useMemo` instead)
- Effects that call parent callback props like `onChange`/`onUpdate` (move to event handler)
- Effects that POST/mutate in response to user action (move to event handler)
- Chains of effects where one sets state that triggers another

## Direct DOM Manipulation

> **References:**
> - [React: Manipulating the DOM with Refs](https://react.dev/learn/manipulating-the-dom-with-refs)
> - [React: Referencing Values with Refs](https://react.dev/learn/referencing-values-with-refs)

Grep patterns:
- `document\.getElementById` — direct DOM access
- `document\.querySelector` — direct DOM query
- `document\.getElementsBy` — direct DOM query
- `\.classList\.` — direct class manipulation

Dangerous ref mutations (modifying React-managed DOM causes crashes):
- `ref\.current\.remove\(\)` — removing a React-managed DOM node
- `ref\.current\.appendChild` — adding children to React-managed DOM
- `ref\.current\.removeChild` — removing children from React-managed DOM
- `ref\.current\.innerHTML` — overwriting React-managed DOM content

## CSS / Tailwind Standards

> **References:**
> - [Tailwind: Managing Duplication](https://tailwindcss.com/docs/styling-with-utility-classes#managing-duplication)
> - [Tailwind: Adding Custom Styles](https://tailwindcss.com/docs/adding-custom-styles)

Grep patterns (for Tailwind projects):
- `style=\{\{` — inline styles mixed with Tailwind
- `bg-\[#` or `text-\[#` — arbitrary values instead of design tokens
- `@apply` in CSS files — de-emphasized in Tailwind v4; prefer extracting components or using `@layer components` with standard CSS properties

## Import Organization

> **References:**
> - [eslint-plugin-import: order](https://github.com/import-js/eslint-plugin-import/blob/main/docs/rules/order.md)

Recommended order (aligns with eslint-plugin-import defaults):
1. Node.js builtins (`path`, `fs` — relevant for SSR/Next.js)
2. External packages (React, third-party libraries)
3. Internal aliases (`@/components`, `~/utils`)
4. Parent directory imports (`../`)
5. Sibling/index imports (`./`)
6. Assets and styles

## Component Size Thresholds

> **References:**
> - [React: Keeping Components Pure](https://react.dev/learn/keeping-components-pure)
> - [React: Extracting State Logic into a Reducer](https://react.dev/learn/extracting-state-logic-into-a-reducer)

| Metric | Good | Flag | Investigate |
|--------|------|------|-------------|
| Lines per component | <150 | 150-300 | >300 |
| useState calls | 1-3 | 4-5 | >5 |
| useEffect calls | 0-1 | 2-3 | >3 |
| Props count | 1-5 | 6-8 | >8 |
| JSX nesting depth | 1-4 | 5-7 | >7 |

## Anti-Patterns to Flag

> **References:**
> - [React: Referencing Values with Refs](https://react.dev/learn/referencing-values-with-refs#differences-between-refs-and-state)
> - [React: Queueing a Series of State Updates](https://react.dev/learn/queueing-a-series-of-state-updates)
> - [React: memo](https://react.dev/reference/react/memo)
> - [React: useCallback](https://react.dev/reference/react/useCallback)
> - [React: useMemo](https://react.dev/reference/react/useMemo)

- Class components (`extends (React\.)?Component` or `extends (React\.)?PureComponent`)
- `forceUpdate` usage
- Stale closures in effects (state variable in effect with empty deps, not using functional update)
- Inline function/object creation in mapped JSX lists (performance concern for large lists)
- Multiple `setState` calls expecting accumulation (use updater function `setCount(c => c + 1)` instead of `setCount(count + 1)` twice)
- `memo` wrapping a component whose parent passes inline `{}` or `() =>` props every render (defeats memoization)
- Reading `ref.current` during render (outside `useEffect`/event handlers) — unpredictable behavior
- `useEffect` with `fetch` that lacks abort/ignore cleanup — race condition on fast re-renders
