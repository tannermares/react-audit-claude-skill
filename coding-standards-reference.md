# React Coding Standards Reference

Standards and grep patterns for auditing React applications.

## Hooks Rules

Grep patterns:
- `if.*\buse[A-Z]` — conditional hook call
- `for.*\buse[A-Z]` or `\.forEach.*\buse[A-Z]` — hook in loop
- `\?\s*use[A-Z]` — ternary hook call
- `&&\s*use[A-Z]` — short-circuit hook call

## Naming Conventions

- Component files: PascalCase (`UserProfile.jsx`)
- Hook files: camelCase with `use` prefix (`useAuth.js`)
- Utility files: camelCase (`formatDate.js`)
- Constants: SCREAMING_SNAKE_CASE (`MAX_RETRIES`)
- Boolean state: `is`/`has`/`should` prefix (`isLoading`, `hasError`)
- Event handler props: `on` prefix (`onClick`, `onItemSelect`)

## State Management

Grep patterns:
- `state\.push\(` or `state\.splice\(` — array mutation
- `state\.\w+\s*=\s*` — object property mutation after useState

Flag:
- `useEffect` that only calls `setState` with data derivable from existing state/props (should derive during render instead)
- Props passed through 3+ component levels (prop drilling)

## Key Props

Grep patterns:
- `key=\{.*index` or `key=\{i\}` or `key=\{idx\}` — index as key
- `\.map\(` in JSX — verify each mapped element has a `key` prop

## Effects

Grep patterns:
- `setInterval` inside `useEffect` — check for cleanup return
- `addEventListener` inside `useEffect` — check for `removeEventListener` cleanup
- `subscribe` inside `useEffect` — check for unsubscribe cleanup

Flag:
- Effects that only transform props into state (derive during render)
- Effects that reset state on prop change (use `key` to reset component)

## Direct DOM Manipulation

Grep patterns:
- `document\.getElementById` — direct DOM access
- `document\.querySelector` — direct DOM query
- `document\.getElementsBy` — direct DOM query
- `\.classList\.` — direct class manipulation

## CSS / Tailwind Standards

Grep patterns (for Tailwind projects):
- `style=\{\{` — inline styles mixed with Tailwind
- `bg-\[#` or `text-\[#` — arbitrary values instead of design tokens
- `@apply` in CSS files — check for overuse

## Import Organization

Recommended order:
1. React/framework imports
2. External library imports
3. Internal components/hooks/utils
4. Assets/styles

## Component Size Thresholds

| Metric | Good | Flag | Investigate |
|--------|------|------|-------------|
| Lines per component | <150 | 150-300 | >300 |
| useState calls | 1-3 | 4-5 | >5 |
| useEffect calls | 0-1 | 2-3 | >3 |
| Props count | 1-5 | 6-8 | >8 |
| JSX nesting depth | 1-4 | 5-7 | >7 |

## Anti-Patterns to Flag

- Class components (`extends (React\.)?Component` or `extends (React\.)?PureComponent`)
- `forceUpdate` usage
- Stale closures in effects (state variable in effect with empty deps, not using functional update)
- Inline function/object creation in mapped JSX lists (performance concern for large lists)
