# React Accessibility (A11y) Audit Checklist

Grep patterns and severity classifications for WCAG 2.1 AA auditing.

## 1. Text Alternatives

Grep patterns:
- `<img(?![^>]*alt=)` — image without `alt`
- `<svg(?![^>]*aria-(label|hidden))` — SVG without accessible name
- `<video(?![^>]*<track)` — video without captions
- `<audio(?![^>]*<track)` — audio without text alternative

Severity: HIGH for meaningful images, LOW for decorative

## 2. Keyboard Navigation

Grep patterns:
- `<div[^>]*onClick` — clickable div (check for `role=` and `onKeyDown`)
- `<span[^>]*onClick` — clickable span (check for `role=` and `onKeyDown`)
- `<a(?![^>]*href)` — anchor without href
- `tabIndex=\{?["']?[1-9]` — positive tabIndex (almost always wrong)

Severity: HIGH for clickable elements without keyboard support

## 3. Focus Management

Grep patterns:
- `role=["']dialog["']` — check for focus trap implementation
- `aria-modal=["']true["']` — check for focus management
- `outline:\s*none` or `outline:\s*0` — focus indicator removed (check for custom replacement)
- `\*:focus\s*\{[^}]*outline:\s*(none|0)` — global focus removal

Severity: HIGH for modals without focus trapping, MEDIUM for missing focus indicators

## 4. Landmarks & Skip Links

Grep patterns:
- Check for `<main>` (should exist exactly once)
- Check for `<nav>`, `<header>`, `<footer>`
- `skip.*main` or `skip.*content` — skip link presence

Severity: MEDIUM for missing landmarks, LOW for missing skip link

## 5. Form Labels & Errors

Grep patterns:
- `<input(?![^>]*(aria-label|id))` — input without label association
- `<select(?![^>]*(aria-label|id))` — select without label
- `<textarea(?![^>]*(aria-label|id))` — textarea without label
- `aria-invalid` — check presence near error-displaying inputs
- `aria-describedby` — check error messages connected to inputs
- `role=["']alert["']` — check form errors use alert role

Severity: HIGH for missing form labels, MEDIUM for unannounced errors

## 6. Semantic HTML

Grep patterns:
- `<div[^>]*class.*["'](header|nav|footer|main|sidebar)` — div instead of semantic element
- Check heading hierarchy: `<h[1-6]` — verify no levels skipped
- `<table` without `<th` — data table missing headers

Severity: MEDIUM for non-semantic structure, LOW for heading hierarchy issues

## 7. ARIA Usage

Check custom interactive components for proper ARIA:
- Dropdowns: `role="listbox"` or `role="combobox"`
- Tabs: `role="tablist"`, `role="tab"`, `role="tabpanel"`
- Toggles: `role="switch"` or `aria-checked`
- Dynamic content: `aria-live` regions
- Disclosure triggers: `aria-expanded`

Severity: HIGH for custom interactive components without ARIA roles

## 8. Screen Reader Support

Grep patterns:
- Icon-only buttons: `<button[^>]*>[\s]*<(svg|img|.*Icon)` — check for `aria-label` or `sr-only` text
- `sr-only` or `visually-hidden` class usage

Severity: HIGH for icon-only buttons without accessible names

## 9. Color & Contrast

Grep patterns:
- `color:\s*["']#(999|aaa|bbb|ccc|ddd|eee)` — potentially low-contrast grays
- `text-(gray|neutral)-(300|400)` — light Tailwind text (check against backgrounds)

Note: Full contrast checking requires runtime tools (Lighthouse, axe-core).

Severity: HIGH for color-only indicators, MEDIUM for potential contrast issues

## 10. autoComplete

Grep patterns:
- `type=["']password["'](?![^>]*autoComplete)` — password without autoComplete
- `type=["']email["'](?![^>]*autoComplete)` — email without autoComplete

Severity: LOW

## Severity Summary

| Level | Examples |
|-------|---------|
| CRITICAL | No keyboard access to essential functionality, focus trapped with no escape |
| HIGH | Clickable elements without keyboard, missing alt on images, no ARIA on custom widgets, icon buttons without names, modals without focus trap, color-only state indicators |
| MEDIUM | Missing landmarks, unannounced form errors, missing aria-live, missing focus indicators, heading hierarchy, non-semantic HTML |
| LOW | Missing skip link, missing autoComplete, decorative images without alt="" |
