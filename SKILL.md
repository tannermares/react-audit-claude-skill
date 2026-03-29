---
name: react-audit
description: Comprehensive React application audit covering coding standards, accessibility, security, performance, testing, and generates results as a Markdown report
user-invocable: true
argument-hint: output-filename
---

# React Application Audit

Perform a comprehensive audit of a React application covering:

1. Coding Standards & Best Practices
2. Component Architecture
3. Accessibility (A11y)
4. Performance
5. Security
6. Testing & Coverage
7. Error Handling & Resilience

## Phase 1: Project Discovery

Detect the following from `package.json`, config files, and source structure:
- React version and meta-framework (Next.js, Remix, Gatsby, Inertia.js, or SPA)
- Build tool (Vite, Webpack, esbuild)
- TypeScript vs JavaScript
- CSS approach (Tailwind, CSS Modules, styled-components, Emotion, Sass)
- Linting (ESLint, Biome, Prettier)
- Test framework (Jest, Vitest, Cypress, Playwright)
- Source root (`src/`, `app/`, `pages/`, etc.)
- Read `.gitignore` to build exclusion list

Gather metrics: component file count, page count, hook count, test file count.

## Phase 2: Coding Standards Audit

Reference: `coding-standards-reference.md`

#### React Patterns
- Hooks called conditionally, in loops, or after early returns
- `.map(` in JSX missing `key=` or using `key={index}`
- Class components (`extends Component`/`PureComponent`)
- Direct DOM manipulation (`document.getElementById`, `document.querySelector`)

#### State Management
- Direct state mutation (`state.push(`, `state.splice(`, `state.field =`)
- Prop drilling (3+ levels)
- Unnecessary state: `useEffect` that only calls `setState` with derived data

#### Styling (adapt to detected CSS approach)
- Tailwind projects: flag `style={{`, hardcoded `bg-[#`, `@apply` overuse
- CSS Modules: check naming conventions
- styled-components: check theme token usage

## Phase 3: Component Architecture Audit

- Flag components >300 lines, >5 useState, >3 useEffect, >8 props
- API calls directly in components vs custom hooks
- Duplicated JSX patterns across files
- TypeScript: search for `: any`, `as any`, `@ts-ignore`, `@ts-expect-error`, excessive `!.` non-null assertions

## Phase 4: Accessibility Audit

Reference: `a11y-checklist.md`

- `<div`/`<span` with `onClick` missing `role=` and `onKeyDown`
- Missing landmarks: `<main>`, `<nav>`, `<header>`, `<footer>`
- Heading hierarchy gaps
- `<img` without `alt`, `<svg` without `aria-label`/`aria-hidden`
- `<input`/`<select`/`<textarea` without associated label
- Icon-only buttons without accessible names
- `role="dialog"` without focus trap
- `outline: none`/`outline: 0` without custom focus indicators
- `tabIndex` > 0

## Phase 5: Performance Audit

- Code splitting: check for `React.lazy`, `import(`, `Suspense` boundaries
- Heavy deps: `moment` (suggest dayjs), full `lodash` (suggest lodash-es)
- Flag >40 production dependencies
- Inline functions/objects in mapped JSX lists
- Images without `loading="lazy"`
- Waterfall fetches (sequential useEffect with fetch/axios)

## Phase 6: Security Audit

Reference: `security-checklist.md`

- `dangerouslySetInnerHTML` — critical if user input flows in
- `eval(`, `new Function(`, `setTimeout(` with string args
- Dynamic `href={`/`src={` without protocol validation
- Hardcoded `API_KEY`, `SECRET`, `TOKEN`, `PASSWORD` string values
- `localStorage.setItem` with auth tokens
- `console.log` in production code
- `postMessage(.*,.*\*)` — wildcard target origin
- `addEventListener.*message` without `event.origin` validation
- Run `npm audit` if package-lock.json exists

## Phase 7: Testing Audit

- Count test files, calculate test-to-component ratio
- Check for coverage config and existing coverage reports
- Flag `getByTestId` overuse (prefer `getByRole`, `getByLabelText`, `getByText`)
- Flag `fireEvent` (prefer `userEvent`)
- Flag snapshot overuse
- Check for missing async handling (`waitFor`, `findBy`)

## Phase 8: Error Handling Audit

- Error boundaries: `componentDidCatch`, `react-error-boundary`, or custom
- Loading states: `isLoading`, `loading`, `pending`, `Skeleton`, `Spinner`
- Empty states: `length === 0`, `!items.length`
- `.catch` or `try...catch` around fetch/API calls
- Client-side form validation and inline error messages

## Phase 9: Generate Report

1. Write findings to markdown using the template in `report-template.md`
2. Score 0-100 based on findings
3. Save as `react-audit-report-[date].md` (or custom filename from argument)
4. Present summary with file location

## Command Usage

When invoked via `/react-audit [filename]`:
- If filename provided: use as output filename (e.g., `my-audit.md`)
- If no filename: use default `react-audit-report-YYYY-MM-DD.md`

## Execution

1. Announce audit start
2. Discover project structure and build exclusion list from `.gitignore`
3. Run each audit phase
4. Generate markdown report
5. Present summary

Respect `.gitignore`. Provide specific file:line references. Include positive findings alongside issues. Adapt checks to the detected framework and CSS approach.
