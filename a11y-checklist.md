# React Accessibility (A11y) Audit Checklist

Grep patterns and severity classifications for WCAG 2.1 AA auditing.

## 1. Text Alternatives

> **References:**
> - [WCAG 1.1.1 Non-text Content](https://www.w3.org/WAI/WCAG21/Understanding/non-text-content.html)
> - [web.dev: Images](https://web.dev/learn/accessibility/images)
> - [web.dev: Video and Audio](https://web.dev/learn/accessibility/video-audio)

Grep patterns:
- `<img(?![^>]*alt=)` — image without `alt`
- `<svg(?![^>]*aria-(label|hidden))` — SVG without accessible name
- `<video(?![^>]*<track)` — video without captions (check child `<track>` elements)
- `<audio(?![^>]*<track)` — audio without text alternative
- `<input[^>]*type=["']image["'](?![^>]*alt=)` — image button without `alt`
- `<canvas(?![^>]*aria-(label|labelledby))` — canvas without accessible name or fallback content
- `role=["']img["'](?![^>]*aria-label)` — custom image role without accessible name

Severity: HIGH for meaningful images, LOW for decorative (use `alt=""` for decorative)

## 2. Keyboard Navigation

> **References:**
> - [WCAG 2.1.1 Keyboard](https://www.w3.org/WAI/WCAG21/Understanding/keyboard.html)
> - [WCAG 4.1.2 Name, Role, Value](https://www.w3.org/WAI/WCAG21/Understanding/name-role-value.html)
> - [web.dev: Keyboard Focus](https://web.dev/learn/accessibility/focus)
> - [WAI-ARIA APG: Button Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/button/)

Grep patterns:
- `<div[^>]*onClick` — clickable div (check for `role=` and `onKeyDown`)
- `<span[^>]*onClick` — clickable span (check for `role=` and `onKeyDown`)
- `<a(?![^>]*href)` — anchor without href
- `tabIndex=\{?["']?[1-9]` — positive tabIndex (almost always wrong)
- `onMouseDown(?![^>]*onKey)` — mousedown without keyboard equivalent
- `onMouseOver(?![^>]*onFocus)` — hover without focus equivalent
- `role=["']button["'](?![^>]*tabIndex)` — custom button not keyboard-focusable (needs `tabIndex="0"`)
- `onDrag|onDrop` — drag-and-drop without keyboard alternative

Severity: CRITICAL for no keyboard access to essential functionality (WCAG 2.1.1 is Level A)

## 3. Focus Management

> **References:**
> - [WCAG 2.4.3 Focus Order](https://www.w3.org/WAI/WCAG21/Understanding/focus-order.html)
> - [WCAG 2.4.7 Focus Visible](https://www.w3.org/WAI/WCAG21/Understanding/focus-visible.html)
> - [WAI-ARIA: Dialog (Modal) Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/)

Grep patterns:
- `role=["']dialog["']` — check for focus trap implementation
- `role=["']dialog["'](?![^>]*aria-(label|labelledby))` — dialog without accessible name
- `aria-modal=["']true["']` — check for focus management
- `outline:\s*none` or `outline:\s*0` — focus indicator removed (check for custom `box-shadow`/`border` replacement)
- `\*:focus\s*\{[^}]*outline:\s*(none|0)` — global focus removal
- `autoFocus` — can disrupt focus order; review for appropriateness
- Check for `:focus-visible` usage — preferred over `:focus` for visible indicators (WCAG technique C45)

Severity: HIGH for modals without focus trapping, HIGH for missing focus indicators (WCAG 2.4.7 is Level AA)

## 4. Landmarks & Skip Links

> **References:**
> - [WCAG 2.4.1 Bypass Blocks](https://www.w3.org/WAI/WCAG21/Understanding/bypass-blocks.html)
> - [WAI-ARIA: Landmark Regions](https://www.w3.org/WAI/ARIA/apg/practices/landmark-regions/)

Grep patterns:
- Check for `<main>` (should exist exactly once)
- Check for `<nav>`, `<header>`, `<footer>`, `<aside>`
- `skip.*main` or `skip.*content` — skip link presence
- Multiple `<nav>` elements — each needs unique `aria-label` to distinguish
- `<section(?![^>]*aria-label)` — sections without label are not landmark regions
- `<form(?![^>]*aria-label)` — forms need accessible names to be landmarks

Severity: HIGH for missing landmarks (WCAG 2.4.1 is Level A), MEDIUM for missing skip link

## 5. Form Labels & Errors

> **References:**
> - [WCAG 1.3.1 Info and Relationships](https://www.w3.org/WAI/WCAG21/Understanding/info-and-relationships.html)
> - [WCAG 3.3.1 Error Identification](https://www.w3.org/WAI/WCAG21/Understanding/error-identification.html)
> - [WCAG 3.3.2 Labels or Instructions](https://www.w3.org/WAI/WCAG21/Understanding/labels-or-instructions.html)
> - [web.dev: Forms](https://web.dev/learn/accessibility/forms)

Grep patterns:
- `<input(?![^>]*(type=["'](hidden|submit|button|reset))|.*(aria-label|aria-labelledby))` — input without accessible label (note: `id` alone is insufficient without a matching `<label for="">`)
- `<select(?![^>]*(aria-label|aria-labelledby))` — select without label
- `<textarea(?![^>]*(aria-label|aria-labelledby))` — textarea without label
- `<label(?![^>]*(for=|htmlFor))` — label not explicitly associated (implicit association requires input nested inside)
- `aria-invalid` — check presence near error-displaying inputs
- `aria-describedby` — check error messages connected to inputs
- `role=["']alert["']` — check form errors use alert role
- `type=["']radio["']` — verify radio groups are wrapped in `<fieldset>` with `<legend>`
- `aria-required` or `required` — check required fields are programmatically indicated

Severity: HIGH for missing form labels, MEDIUM for unannounced errors

## 6. Semantic HTML

> **References:**
> - [WCAG 1.3.1 Info and Relationships](https://www.w3.org/WAI/WCAG21/Understanding/info-and-relationships.html)
> - [web.dev: Document Structure](https://web.dev/learn/accessibility/structure)

Grep patterns:
- `<div[^>]*class.*["'](header|nav|footer|main|sidebar)` — div instead of semantic element
- Check heading hierarchy: `<h[1-6]` — verify no levels skipped (e.g. `<h3>` without preceding `<h2>`)
- `<table` without `<th` — data table missing headers (exclude `role="presentation"` layout tables)
- `<th(?![^>]*scope=)` — table headers without `scope` attribute for row/column association

Severity: MEDIUM for non-semantic structure and heading hierarchy (WCAG 1.3.1 is Level A)

## 7. ARIA Usage

> **References:**
> - [WAI-ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/)
> - [WCAG 4.1.2 Name, Role, Value](https://www.w3.org/WAI/WCAG21/Understanding/name-role-value.html)
> - [First Rule of ARIA Use](https://www.w3.org/TR/using-aria/#firstrule)
> - [WAI-ARIA APG: Combobox Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/)
> - [WAI-ARIA APG: Listbox Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/listbox/)
> - [WAI-ARIA APG: Tabs Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/)
> - [WAI-ARIA APG: Switch Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/switch/)
> - [WAI-ARIA APG: Disclosure Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/)

Check custom interactive components for proper ARIA:
- Dropdowns: `role="listbox"` or `role="combobox"`
- Tabs: `role="tablist"`, `role="tab"`, `role="tabpanel"`
- Toggles: `role="switch"` or `aria-checked`
- Dynamic content: `aria-live` regions
- Disclosure triggers: `aria-expanded`

Required attributes per APG pattern (grep for role without required attr):
- `role=["']combobox["'](?![^>]*aria-expanded)` — combobox must have `aria-expanded`
- `role=["']combobox["'](?![^>]*aria-controls)` — combobox must reference its popup
- `role=["']tab["'](?![^>]*aria-selected)` — tabs must indicate selection state
- `role=["']tab["'](?![^>]*aria-controls)` — tabs must reference their panel
- `role=["']tabpanel["'](?![^>]*aria-labelledby)` — panels must reference their tab
- `role=["']switch["'](?![^>]*aria-checked)` — switch must have checked state
- `role=["']listbox["'](?![^>]*aria-(label|labelledby))` — listbox needs accessible name
- `role=["']option["'](?![^>]*aria-selected)` — options need selection state
- `aria-hidden=["']true["']` on focusable elements — critical disconnect (check for `tabIndex`, `href`, `<button>`, `<input>` nearby)

Severity: HIGH for custom interactive components without ARIA roles or missing required attributes

## 8. Screen Reader Support

> **References:**
> - [WCAG 4.1.2 Name, Role, Value](https://www.w3.org/WAI/WCAG21/Understanding/name-role-value.html)
> - [web.dev: ARIA and HTML](https://web.dev/learn/accessibility/aria-html)

Grep patterns:
- Icon-only buttons: `<button[^>]*>[\s]*<(svg|img|.*Icon)` — check for `aria-label` or `sr-only` text
- Icon-only links: `<a[^>]*>[\s]*<(svg|img|.*Icon)` — same check for links
- Empty buttons: `<button[^>]*>\s*</button>` — button with no text and no `aria-label`
- Empty links: `<a[^>]*href[^>]*>\s*</a>` — link with no text content
- `sr-only` or `visually-hidden` class usage
- `aria-hidden=["']true["']` on meaningful visible content — hidden from screen readers but visible

Severity: HIGH for icon-only buttons/links without accessible names

## 9. Color & Contrast

> **References:**
> - [WCAG 1.4.3 Contrast (Minimum)](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
> - [WCAG 1.4.1 Use of Color](https://www.w3.org/WAI/WCAG21/Understanding/use-of-color.html)
> - [web.dev: Color and Contrast](https://web.dev/learn/accessibility/color-contrast)

Grep patterns:
- `color:\s*["']?#(999|aaa|bbb|ccc|ddd|eee|999999|aaaaaa|bbbbbb|cccccc|dddddd|eeeeee)` — potentially low-contrast grays
- `text-(gray|neutral)-(300|400)` — light Tailwind text (check against backgrounds)
- `placeholder` text styling — placeholders often fail contrast requirements
- `opacity:\s*0\.[0-4]` — low opacity text potentially failing contrast

Note: Full contrast checking requires runtime tools (Lighthouse, axe-core). Large text (18pt/24px or 14pt bold/18.5px) only needs 3:1 ratio vs 4.5:1 for normal text.

Severity: HIGH for color-only indicators, MEDIUM for potential contrast issues

## 10. autoComplete

> **References:**
> - [WCAG 1.3.5 Identify Input Purpose](https://www.w3.org/WAI/WCAG21/Understanding/identify-input-purpose.html)
> - [MDN: autocomplete Attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete)

Grep patterns:
- `type=["']password["'](?![^>]*autoComplete)` — password without autoComplete
- `type=["']email["'](?![^>]*autoComplete)` — email without autoComplete
- `type=["']tel["'](?![^>]*autoComplete)` — telephone without autoComplete
- `autoComplete=["']off["']` — sometimes set inappropriately, especially on login forms

Severity: MEDIUM (WCAG 1.3.5 is Level AA)

## Severity Summary

Aligned with WCAG conformance levels: Level A violations → CRITICAL/HIGH, Level AA → HIGH/MEDIUM.

| Level | Examples |
|-------|---------|
| CRITICAL | No keyboard access to essential functionality (2.1.1 Level A), focus trapped with no escape, `aria-hidden="true"` on focusable elements |
| HIGH | Clickable elements without keyboard equivalent, missing alt on meaningful images, no ARIA on custom widgets, icon buttons/links without names, modals without focus trap, missing focus indicators (2.4.7 Level AA), missing landmarks (2.4.1 Level A), color-only state indicators |
| MEDIUM | Missing skip link, unannounced form errors, missing aria-live, heading hierarchy gaps, non-semantic HTML, missing autoComplete (1.3.5 Level AA), potential contrast issues |
| LOW | Decorative images without `alt=""`, redundant ARIA on native elements |
