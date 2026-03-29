---
name: react-audit
description: Comprehensive React application audit covering coding standards, accessibility, security, performance, testing, and generates results as a Markdown report
user-invocable: true
argument-hint: output-filename
---

# React Application Audit

You are performing a comprehensive audit of a React application. This audit will cover:

1. **Coding Standards & Best Practices**
2. **Component Architecture**
3. **Accessibility (A11y)**
4. **Performance**
5. **Security**
6. **Testing & Coverage**
7. **Error Handling & Resilience**

## Audit Process

### Phase 1: Project Discovery & Setup

1. **Identify Project Structure**
   - Read `package.json` to understand dependencies and React version
   - Detect build tool: look for `vite.config.*`, `webpack.config.*`, `next.config.*`, `remix.config.*`
   - Check for TypeScript: look for `tsconfig.json`, count `.ts`/`.tsx` vs `.js`/`.jsx` files
   - Detect linting: look for `eslint.config.*`, `.eslintrc.*`, `.prettierrc*`, `biome.json`
   - Detect test framework: look for `jest.config.*`, `vitest.config.*`, `cypress.config.*`, `playwright.config.*`

2. **Detect CSS Approach**
   - Check for Tailwind: `@tailwindcss/vite` or `tailwindcss` in deps, `tailwind.config.*`, `@theme` in CSS files
   - Check for CSS Modules: `*.module.css` files
   - Check for CSS-in-JS: `styled-components`, `@emotion` in deps
   - Check for preprocessors: `*.scss`, `*.less` files

3. **Detect Meta-Framework**
   - `next.config.*` → Next.js
   - `@inertiajs/react` in deps → Inertia.js
   - `@remix-run/*` in deps → Remix
   - `gatsby-config.*` → Gatsby
   - None → Standard SPA

4. **Establish Source Root**
   - Auto-detect: `src/`, `app/javascript/`, `app/`, `pages/`
   - Read `.gitignore` to build exclusion list

5. **Gather Metrics**
   - Count total component files (`.jsx`, `.tsx`)
   - Count page files, hook files, utility files
   - Count test files (`*.test.*`, `*.spec.*`, `__tests__/`)

### Phase 2: Coding Standards & Best Practices Audit

Evaluate code against React community standards. Reference: `coding-standards-reference.md`

#### React Patterns
- **Hooks rules**: Search for hooks called conditionally (`if.*use[A-Z]`), in loops, or after early returns
- **Key props**: Search for `.map(` in JSX and verify `key=` is present; flag `key={index}` or `key={i}`
- **Class components**: Search for `extends (React\.)?Component` or `extends (React\.)?PureComponent`
- **Direct DOM manipulation**: Search for `document.getElementById`, `document.querySelector` inside components
- **forceUpdate**: Search for `forceUpdate` usage

#### State Management
- **Direct state mutation**: Search for patterns like `state.push(`, `state.splice(`, `state.field =`
- **Prop drilling**: Identify props passed through 3+ component levels
- **Unnecessary state**: Look for `useEffect` that only calls `setState` with derived data
- **Context usage**: Check for React Context vs prop drilling for widely-shared state

#### Component Organization
- **File naming**: Verify components use PascalCase filenames
- **Consistent extensions**: Check for mixed `.jsx`/`.tsx` (should be consistent)
- **Import organization**: Check for consistent import ordering

#### Styling Consistency (adapt to detected CSS approach)
- If Tailwind: flag `style={{` inline styles, hardcoded color values `bg-[#`, `@apply` overuse
- If CSS Modules: check naming conventions
- If styled-components: check for theme token usage

### Phase 3: Component Architecture Audit

#### Component Size & Complexity
- Count lines per component file (flag >300 lines)
- Count `useState` calls per component (flag >5)
- Count `useEffect` calls per component (flag >3)
- Estimate props count per component (flag >8)

#### Separation of Concerns
- Check for API calls (`fetch(`, `axios.`, `useSWR`, `useQuery`) directly in components vs custom hooks
- Look for custom hooks directory or `use*.{js,jsx,ts,tsx}` files
- Flag business logic mixed with rendering (complex expressions inside JSX return)

#### Reusability
- Identify duplicated JSX patterns across files
- Check for hardcoded strings that should be constants
- Look for opportunities to extract shared components or hooks

#### TypeScript Quality (if TypeScript project)
- Search for `: any`, `as any`, `<any>` (flag each occurrence)
- Search for `@ts-ignore` and `@ts-expect-error`
- Search for `!.` and `![` non-null assertions (flag excessive use)
- Check for shared type definition files

### Phase 4: Accessibility (A11y) Audit

Reference: `a11y-checklist.md`

#### Semantic HTML
- Search for `<div` with `onClick` — check for `role=` and `onKeyDown`
- Search for `<span` with `onClick` — check for `role=` and keyboard support
- Check for landmark elements: `<main>`, `<nav>`, `<header>`, `<footer>`
- Check heading hierarchy: `<h1>` through `<h6>` — verify no levels skipped

#### ARIA Attributes
- Check custom interactive components (modals, dropdowns, tabs) for `role=` and `aria-*`
- Search for `role="dialog"` — verify `aria-modal`, focus trap
- Search for dynamic content areas — check for `aria-live` regions
- Check `aria-expanded` on disclosure triggers

#### Keyboard Navigation
- Search for `onClick` on non-button/link elements — check for `onKeyDown`
- Search for `tabIndex` > 0 (anti-pattern)
- Check modals for focus trapping (look for focus-trap libraries or manual implementation)
- Search for `outline: none` or `outline: 0` — verify custom focus indicators exist

#### Images & Media
- Search for `<img` without `alt` attribute
- Search for `<svg` without `aria-label` or `aria-hidden`
- Check icon-only buttons for accessible names (`aria-label` or sr-only text)

#### Forms
- Search for `<input` without associated `<label>` or `aria-label`
- Check for `aria-invalid` and `aria-describedby` on error-prone inputs
- Search for `type="password"` — check for `autoComplete` attribute

### Phase 5: Performance Audit

#### Bundle Size & Code Splitting
- Read build config for `manualChunks` or `splitChunks`
- Search for `React.lazy` and `import(` for code splitting
- Check for `Suspense` boundaries wrapping lazy components
- Count production dependencies (flag >40)
- Flag heavy dependencies: `moment` (suggest dayjs), full `lodash` (suggest lodash-es)

#### Rendering Optimization
- Search for `React.memo` usage on list items and frequently-rendered components
- Search for inline function definitions in JSX: `onClick={() =>` in mapped lists
- Search for inline object/array literals in JSX: `style={{`, `data={[`
- Check for `useMemo` and `useCallback` usage patterns

#### Assets
- Search for `<img` without `loading="lazy"` attribute
- Check for image optimization (next/image, vite-plugin-imagemin, etc.)
- Check SVG strategy (inline components, sprite sheets, etc.)

#### Network
- Check for waterfall fetches (sequential `useEffect` with `fetch`/`axios`)
- Look for data fetching libraries with caching (React Query, SWR)
- Check for request deduplication patterns

### Phase 6: Security Audit

Reference: `security-checklist.md`

#### XSS Vectors
- Search for `dangerouslySetInnerHTML` — flag every usage, critical if user input flows in
- Search for `innerHTML` in vanilla JS within the project
- Search for `document.write`
- Search for template literal HTML injection

#### URL Injection
- Search for `href={` with dynamic values — check for `javascript:` protocol risk
- Search for `src={` with user-controlled values
- Search for `window.location` assignments with user input

#### Sensitive Data Exposure
- Search for `API_KEY`, `SECRET`, `TOKEN`, `PASSWORD` as variable names with string values
- Search for `localStorage.setItem` with token/auth data
- Search for `console.log` in production code
- Check `.env` files are in `.gitignore`

#### Third-Party Risks
- Run `npm audit` if package-lock.json exists
- Search for `eval(`, `new Function(`, `setTimeout(` with string args
- Search for `addEventListener.*message` — check for `event.origin` validation
- Search for `postMessage(.*,.*\*)` — wildcard target origin

#### CSRF & Auth
- Check how auth tokens are stored and sent
- For Inertia.js: verify CSRF token handling via Rails middleware
- Check for `credentials: 'include'` or `withCredentials: true` on cross-origin requests

### Phase 7: Testing & Coverage Audit

#### Test Framework Detection
- Identify test framework from config files and package.json
- Count test files: `**/*.test.*`, `**/*.spec.*`, `**/__tests__/**`
- Calculate test-to-component ratio

#### Coverage Assessment
- Look for coverage config in test framework config or package.json
- Check for coverage output directory (`coverage/`)
- If coverage report exists, read and summarize

#### Test Quality (if tests exist)
- Search for `getByTestId` — flag overuse (prefer semantic queries)
- Search for `getByRole`, `getByLabelText`, `getByText` — preferred queries
- Search for `fireEvent` vs `userEvent` — userEvent preferred
- Check for snapshot overuse: `toMatchSnapshot`, `toMatchInlineSnapshot`

#### Test Anti-Patterns
- Search for overly broad mocking
- Look for tests checking implementation details (state, internal methods)
- Check for missing async handling (`waitFor`, `findBy`)

### Phase 8: Error Handling & Resilience Audit

#### Error Boundaries
- Search for `componentDidCatch`, `react-error-boundary`, or custom error boundary components
- Check if error boundaries wrap major route sections
- Check for granular error boundaries (not just one at root)

#### Loading States
- Search for loading indicators: `isLoading`, `loading`, `pending`, `Skeleton`, `Spinner`
- Verify async operations show loading state

#### Empty States
- Search for empty state handling: `length === 0`, `!items.length`, "no results"
- Verify lists handle zero-item case

#### Network Error Handling
- Search for `.catch` or `try...catch` around fetch/API calls
- Check for retry logic
- Search for error state rendering after failed requests

#### Form Validation
- Check for client-side validation before submission
- Look for inline error messages
- Check validation timing (blur vs submit)

### Phase 9: Generate Audit Report

After completing all phases, generate a comprehensive report:

1. **Create Markdown Report**: Write findings to a markdown file using the template in `report-template.md`

2. **Scoring**:
   - Calculate overall score (0-100) based on findings
   - 90-100: Excellent
   - 80-89: Good
   - 70-79: Fair
   - 60-69: Needs Attention
   - <60: Critical

3. **Output Location**: Save as `react-audit-report-[date].md` in project root (or custom filename from argument)

## Tool Usage Guidelines

1. **Respect .gitignore**: Before scanning, read `.gitignore` and exclude listed files/directories
2. **Use Glob for file discovery**: Find JSX/TSX files, config files, test files
3. **Use Grep for pattern matching**: Search for anti-patterns, vulnerabilities, a11y issues
4. **Use Read for detailed analysis**: Read configuration files, critical component files
5. **Generate statistics**: Count files, lines, calculate metrics
6. **Use Bash sparingly**: Only for `npm audit`, `wc -l`, and similar system commands

## Command Usage

When invoked via `/react-audit [filename]`:
- If filename provided: Use as output filename (e.g., `my-audit.md`)
- If no filename: Use default `react-audit-report-YYYY-MM-DD.md`

## Execution Workflow

1. Announce audit start and estimated scope
2. Read `.gitignore` to build exclusion list
3. Discover project structure (files, directories, framework)
4. Run each audit phase sequentially
5. Collect findings and metrics
6. Generate markdown report with findings
7. Present summary and provide file location

## Important Notes

- Be thorough but respect the .gitignore file
- Provide specific file and line references for issues
- Give constructive recommendations, not just criticism
- Prioritize findings by severity
- Include both positive findings and issues
- Make the report actionable with clear next steps
- Include code snippets in findings where relevant (but keep sections readable)
- Adapt checks to the detected framework and CSS approach
- Do NOT generate PDF — output Markdown only

---

Begin the comprehensive React application audit now. Work through each phase systematically and generate a professional audit report.
