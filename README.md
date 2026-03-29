# React Audit Skill

Comprehensive React application audit skill for Claude Code.

## Overview

This skill performs a thorough audit of your React application covering:

- **Coding Standards**: React best practices, hooks rules, component patterns
- **Component Architecture**: Complexity, separation of concerns, reusability
- **Accessibility (A11y)**: WCAG 2.1 AA compliance, semantic HTML, ARIA, keyboard navigation
- **Performance**: Bundle size, code splitting, rendering optimization, network patterns
- **Security**: XSS, URL injection, sensitive data exposure, third-party risks
- **Testing & Coverage**: Test quality, coverage gaps, anti-patterns
- **Error Handling**: Error boundaries, loading states, empty states, form validation

## Usage

### Basic Usage

```bash
/react-audit
```

This will generate a report with default filename: `react-audit-report-YYYY-MM-DD.md`

### Custom Filename

```bash
/react-audit my-custom-report
```

This will generate: `my-custom-report.md`

## Output

The skill generates a **Markdown report** with detailed findings organized by category, severity levels, and actionable recommendations.

## Report Structure

1. **Executive Summary**: Overall health score and critical issues
2. **Audit Scope**: Files analyzed, stack detected, project stats
3. **Coding Standards**: Style violations, hooks issues, anti-patterns
4. **Component Architecture**: Complexity metrics, design patterns
5. **Accessibility Assessment**: A11y issues by severity with WCAG references
6. **Testing & Coverage**: Coverage metrics and gaps
7. **Security Assessment**: Vulnerabilities by severity
8. **Performance Analysis**: Bundle, rendering, and network issues
9. **Error Handling & Resilience**: Coverage of error states
10. **Detailed Findings**: Critical issues with file references
11. **Prioritized Recommendations**: Immediate, short-term, and long-term actions
12. **Conclusion**: Overall assessment and next steps

## Supported Frameworks

The skill auto-detects and adapts to your stack:

- **React** (any version, including React 19)
- **Build Tools**: Vite, Webpack, esbuild
- **Meta-Frameworks**: Next.js, Inertia.js, Remix, Gatsby
- **CSS**: Tailwind CSS, CSS Modules, styled-components, Emotion, Sass
- **Languages**: JavaScript, TypeScript
- **Test Frameworks**: Jest, Vitest, Cypress, Playwright, React Testing Library

## What Gets Audited

### Files Included
- All `.jsx`, `.tsx`, `.js`, `.ts` files in source directories
- CSS/SCSS files and Tailwind configuration
- Build configuration files
- Test files
- `package.json` and dependency configuration

### Files Excluded (respects .gitignore)
- `node_modules/`
- `dist/`, `build/`, `.next/`
- `coverage/`
- `vendor/`
- Any paths listed in `.gitignore`

## Audit Criteria

### Coding Standards
Based on:
- [React documentation](https://react.dev/) best practices
- Rules of Hooks
- Community conventions from major React codebases

### Accessibility Standards
Based on:
- WCAG 2.1 Level AA
- WAI-ARIA Authoring Practices
- React accessibility documentation

### Security Standards
Based on:
- OWASP client-side security guidelines
- React security best practices
- npm audit vulnerability database

## Severity Levels

🔴 **Critical** (Fix Immediately)
- Security vulnerabilities, a11y blockers, data exposure

🟠 **High** (Fix This Week)
- Major a11y gaps, performance bottlenecks, missing error handling

🟡 **Medium** (Fix This Sprint)
- Code quality, test gaps, minor a11y, tech debt

🟢 **Low** (Nice to Have)
- Style consistency, minor optimizations, documentation

## Customization

To customize the audit criteria, edit:
- `SKILL.md`: Main audit workflow
- `coding-standards-reference.md`: Coding standards to check
- `security-checklist.md`: Security vulnerabilities to scan
- `a11y-checklist.md`: Accessibility checks to perform

## Tips

1. **Run Before Major Releases**: Audit before deploying to production
2. **Regular Audits**: Run monthly or quarterly to catch technical debt
3. **Team Reviews**: Share the report with your team for prioritization
4. **Track Progress**: Run periodically and compare scores over time
5. **Focus on Critical First**: Address critical and high-priority issues immediately

## Troubleshooting

### "Command not found: /react-audit"
- Restart Claude Code or VSCode
- Check skill is in: `~/.claude/skills/react-audit/SKILL.md`

### Audit takes too long
- Check .gitignore includes: `node_modules/`, `dist/`, `build/`
- Large projects (>100k LOC) may take several minutes

## Support

For issues or improvements:
- Report issues: https://github.com/tannermares/react-audit-claude-skill/issues

## Inspiration

This skill was inspired by the [Rails Audit Claude Skill](https://github.com/jcuervo/rails-audit-claude-skill) by [@jcuervo](https://github.com/jcuervo). The original rails-audit skill provided the framework for organizing audit phases, severity levels, and report templates that this React-focused version adapts for React applications.

## Version

Current Version: 1.0.0
Last Updated: 2026-03-29
