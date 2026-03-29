# React Accessibility (A11y) Audit Checklist

Accessibility checklist for React applications, organized by WCAG 2.1 AA principles.

## POUR Principles

### 1. Perceivable

#### 1.1 Text Alternatives

```jsx
// VULNERABLE - Image without alt text
<img src={albumArt} />

// VULNERABLE - Empty alt on meaningful image
<img src={albumArt} alt="" />

// SAFE - Descriptive alt text
<img src={albumArt} alt="Album artwork for Midnight Sessions" />

// SAFE - Decorative image explicitly marked
<img src={divider} alt="" role="presentation" />

// VULNERABLE - SVG without accessible name
<svg viewBox="0 0 24 24"><path d="..." /></svg>

// SAFE - SVG with aria-label or aria-hidden
<svg aria-label="Settings" role="img"><path d="..." /></svg>
<svg aria-hidden="true"><path d="..." /></svg>  // decorative
```

#### Grep Patterns:
- `<img(?![^>]*alt=)` - `<img>` without `alt` attribute
- `<svg(?![^>]*aria-(label|hidden))` - `<svg>` without accessible name
- `<video(?![^>]*<track)` - `<video>` without captions track
- `<audio(?![^>]*<track)` - `<audio>` without text alternative

#### Severity: HIGH for missing alt on meaningful images, LOW for decorative images

---

#### 1.2 Color and Contrast

**Note**: Color contrast cannot be fully audited from code alone. Recommend running Lighthouse or axe-core for runtime checks.

```jsx
// FLAG - Hardcoded low-contrast colors
<span style={{ color: '#999' }}>Important text on white bg</span>

// FLAG - Color as only indicator
<span style={{ color: 'red' }}>Required field</span>

// BETTER - Color + text/icon indicator
<span className="text-red-500">* Required</span>
<span className="sr-only">Required field</span>
```

#### Grep Patterns:
- Check Tailwind config for color tokens that may have low contrast
- `color:\s*["']#(999|aaa|bbb|ccc|ddd|eee)` - Potentially low-contrast grays
- `text-(gray|neutral)-(300|400)` - Light Tailwind text colors (check against backgrounds)

#### Severity: HIGH for color-only indicators, MEDIUM for potential contrast issues

---

### 2. Operable

#### 2.1 Keyboard Navigation

```jsx
// VULNERABLE - Clickable div without keyboard support
<div onClick={handleClick}>Click me</div>

// VULNERABLE - Clickable span without role or keyboard
<span onClick={toggleMenu}>Menu</span>

// SAFE - Use native button
<button onClick={handleClick}>Click me</button>

// SAFE - If div must be used, add role and keyboard handler
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => { if (e.key === 'Enter' || e.key === ' ') handleClick(); }}
>
  Click me
</div>
```

#### Grep Patterns:
- `<div[^>]*onClick` - Clickable div (check for `role=` and `onKeyDown`)
- `<span[^>]*onClick` - Clickable span (check for `role=` and `onKeyDown`)
- `<a(?![^>]*href)` - Anchor without href
- `tabIndex=["']\{?-?[2-9]` or `tabIndex=["']\{?\d{2}` - tabIndex > 1 (anti-pattern)
- `tabIndex=\{?["']?[1-9]` - Positive tabIndex (almost always wrong)

#### Severity: HIGH for clickable non-interactive elements without keyboard support

---

#### 2.2 Focus Management

```jsx
// CHECK - Modal focus trapping
// When a modal opens, focus should move to the modal
// When it closes, focus should return to the trigger element

// VULNERABLE - No focus management in modal
const Modal = ({ isOpen, children }) => {
  if (!isOpen) return null;
  return <div className="modal">{children}</div>;
};

// SAFE - Focus trap in modal
import { FocusTrap } from 'focus-trap-react';
const Modal = ({ isOpen, children }) => {
  if (!isOpen) return null;
  return (
    <FocusTrap>
      <div className="modal" role="dialog" aria-modal="true">
        {children}
      </div>
    </FocusTrap>
  );
};
```

#### Grep Patterns:
- `role=["']dialog["']` then check for focus trap implementation
- `aria-modal=["']true["']` then check for focus management
- `outline:\s*none` or `outline:\s*0` - Focus indicator removed (check for custom replacement)
- `\*:focus\s*\{[^}]*outline:\s*(none|0)` - Global focus removal

#### Severity: HIGH for modals without focus trapping, MEDIUM for missing focus indicators

---

#### 2.3 Skip Links and Landmarks

```jsx
// CHECK - Skip navigation link
<a href="#main-content" className="sr-only focus:not-sr-only">
  Skip to main content
</a>

// CHECK - Landmark regions
<header>...</header>        // banner landmark
<nav>...</nav>              // navigation landmark
<main id="main-content">   // main landmark
  <section>...</section>    // region landmark (needs aria-label)
</main>
<footer>...</footer>        // contentinfo landmark
```

#### Grep Patterns:
- Check for `<main>` element (should exist exactly once)
- Check for `<nav>` element (should exist for navigation)
- Check for `<header>` and `<footer>` elements
- `skip.*main` or `skip.*content` - Skip link presence

#### Severity: MEDIUM for missing landmarks, LOW for missing skip link

---

### 3. Understandable

#### 3.1 Form Labels and Errors

```jsx
// VULNERABLE - Input without label
<input type="text" placeholder="Enter name" />

// VULNERABLE - Label not associated
<label>Name</label>
<input type="text" />

// SAFE - Associated label
<label htmlFor="name">Name</label>
<input id="name" type="text" />

// SAFE - aria-label for icon-only inputs
<input type="search" aria-label="Search albums" />

// VULNERABLE - Error not announced
{error && <span className="text-red-500">{error}</span>}

// SAFE - Error connected to input
<input id="email" aria-invalid={!!error} aria-describedby="email-error" />
{error && <span id="email-error" role="alert">{error}</span>}
```

#### Grep Patterns:
- `<input(?![^>]*(aria-label|id))` - Input without label association
- `<select(?![^>]*(aria-label|id))` - Select without label
- `<textarea(?![^>]*(aria-label|id))` - Textarea without label
- `aria-invalid` - Check presence near error-displaying inputs
- `aria-describedby` - Check error messages are connected to inputs
- `role=["']alert["']` - Check form errors use alert role

#### Severity: HIGH for missing form labels, MEDIUM for unannounced errors

---

#### 3.2 Consistent Navigation

```jsx
// CHECK - autoComplete on login/registration forms
<input type="email" autoComplete="email" />
<input type="password" autoComplete="current-password" />
<input type="password" autoComplete="new-password" />  // registration
<input type="text" autoComplete="name" />
<input type="tel" autoComplete="tel" />
```

#### Grep Patterns:
- `type=["']password["'](?![^>]*autoComplete)` - Password input without autoComplete
- `type=["']email["'](?![^>]*autoComplete)` - Email input without autoComplete

#### Severity: LOW

---

### 4. Robust

#### 4.1 Semantic HTML

```jsx
// VULNERABLE - Non-semantic structure
<div className="header">...</div>
<div className="nav">...</div>
<div className="main">...</div>

// SAFE - Semantic elements
<header>...</header>
<nav aria-label="Main navigation">...</nav>
<main>...</main>

// CHECK - Heading hierarchy
<h1>Page Title</h1>        // One per page
  <h2>Section</h2>         // No skipping levels
    <h3>Subsection</h3>
  <h2>Another Section</h2>

// VULNERABLE - Skipped heading level
<h1>Title</h1>
<h3>Subsection</h3>  // Skipped h2!
```

#### Grep Patterns:
- `<div[^>]*class.*["'](header|nav|footer|main|sidebar)` - Div used instead of semantic element
- Check heading hierarchy: `<h[1-6]` — verify no levels are skipped
- `<table` without `<th` — data table missing headers

#### Severity: MEDIUM for non-semantic structure, LOW for heading hierarchy issues

---

#### 4.2 ARIA Usage

```jsx
// CHECK - Custom components need ARIA
// Dropdown/select
<div role="listbox" aria-label="Select genre" aria-expanded={isOpen}>
  <div role="option" aria-selected={selected}>Rock</div>
</div>

// Tabs
<div role="tablist">
  <button role="tab" aria-selected={active} aria-controls="panel-1">Tab 1</button>
</div>
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">...</div>

// Toggle/switch
<button role="switch" aria-checked={isOn} aria-label="Dark mode">
  Toggle
</button>

// Loading states
<div aria-busy={isLoading} aria-live="polite">
  {isLoading ? 'Loading...' : content}
</div>
```

#### Grep Patterns:
- Custom dropdown/select components: check for `role="listbox"` or `role="combobox"`
- Custom tab components: check for `role="tablist"`, `role="tab"`, `role="tabpanel"`
- Toggle components: check for `role="switch"` or `aria-checked`
- Dynamic content areas: check for `aria-live` regions
- `aria-expanded` - Should be present on disclosure triggers (dropdowns, accordions)

#### Severity: HIGH for custom interactive components without ARIA roles

---

## Screen Reader Specific Checks

### Visually Hidden Text
```jsx
// CHECK - sr-only class for screen reader text
<button>
  <TrashIcon />
  <span className="sr-only">Delete track</span>
</button>

// CHECK - Icon-only buttons need accessible names
<button aria-label="Close dialog">
  <XIcon />
</button>
```

#### Grep Patterns:
- Icon-only buttons: `<button[^>]*>[\s]*<(svg|img|.*Icon)` — check for `aria-label` or `sr-only` text
- `sr-only` or `visually-hidden` class usage

#### Severity: HIGH for icon-only buttons without accessible names

---

## Accessibility Severity Levels

### CRITICAL (Fix Immediately)
- No keyboard access to essential functionality
- Focus trapped with no escape
- Missing form labels on critical inputs (login, registration)

### HIGH (Fix This Week)
- Clickable elements without keyboard support
- Images without alt text (meaningful images)
- Custom interactive components without ARIA roles
- Icon-only buttons without accessible names
- Modals without focus trapping
- Color as the only indicator of state

### MEDIUM (Fix This Sprint)
- Missing landmark regions (`<main>`, `<nav>`)
- Form errors not connected to inputs
- Missing `aria-live` for dynamic content
- Missing focus indicators (outline removed)
- Heading hierarchy issues
- Non-semantic HTML structure

### LOW (Nice to Have)
- Missing skip link
- Missing `autoComplete` on form inputs
- Decorative images without `alt=""`
- Missing `lang` attribute on `<html>`
- Tab order could be improved

---

## Runtime Testing Recommendations

These checks cannot be done from code alone — recommend running:

1. **Lighthouse Accessibility Audit** — automated WCAG checks
2. **axe DevTools** — browser extension for detailed a11y scanning
3. **Keyboard testing** — tab through entire app manually
4. **Screen reader testing** — VoiceOver (Mac) or NVDA (Windows)
5. **Color contrast checker** — verify all text meets WCAG AA (4.5:1 normal, 3:1 large)
