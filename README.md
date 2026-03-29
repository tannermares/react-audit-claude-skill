# React Audit Skill

A Claude Code skill that performs a comprehensive audit of your React application and generates a Markdown report.

## Safety

This skill **only reads** your code and **writes one file** (the markdown report). It runs `npm audit` (read-only) but makes no changes to your project, dependencies, or configuration.

## Install

```bash
git clone https://github.com/tannermares/react-audit-claude-skill.git ~/.claude/skills/react-audit
```

## Usage

```bash
/react-audit              # generates react-audit-report-YYYY-MM-DD.md
/react-audit my-report    # generates my-report.md
```

## What It Checks

- **Coding Standards** — hooks rules, component patterns, state management, naming
- **Component Architecture** — complexity, separation of concerns, reusability, TypeScript quality
- **Accessibility** — WCAG 2.1 AA, semantic HTML, ARIA, keyboard navigation, forms
- **Performance** — bundle size, code splitting, rendering optimization, network patterns
- **Security** — XSS, URL injection, sensitive data exposure, third-party risks
- **Testing** — coverage gaps, test quality, anti-patterns
- **Error Handling** — error boundaries, loading/empty states, form validation

## Auto-Detected Stacks

React (any version), Next.js, Remix, Gatsby, Inertia.js, Vite, Webpack, Tailwind CSS, CSS Modules, styled-components, TypeScript, Jest, Vitest, Cypress, Playwright

## Customization

Edit these files in `~/.claude/skills/react-audit/` to adjust audit criteria:

- `coding-standards-reference.md` — React best practices and thresholds
- `security-checklist.md` — security vulnerability patterns
- `a11y-checklist.md` — accessibility checks
- `report-template.md` — report structure

## Issues

https://github.com/tannermares/react-audit-claude-skill/issues

## Inspiration

Inspired by the [Rails Audit Claude Skill](https://github.com/jcuervo/rails-audit-claude-skill) by [@jcuervo](https://github.com/jcuervo).

## License

MIT
