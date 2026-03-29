# React Security Audit Checklist

Comprehensive security checklist for React applications.

## React Security Vulnerabilities

### 1. Cross-Site Scripting (XSS)

#### What to Look For:
```jsx
// VULNERABLE - dangerouslySetInnerHTML with user input
<div dangerouslySetInnerHTML={{ __html: userComment }} />

// VULNERABLE - innerHTML in vanilla JS
element.innerHTML = userInput;

// VULNERABLE - document.write
document.write(userInput);

// SAFE - React auto-escapes by default
<div>{userComment}</div>

// SAFE - Sanitize when HTML rendering is needed
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(content) }} />
```

#### Grep Patterns:
- `dangerouslySetInnerHTML` - Every usage needs review
- `\.innerHTML\s*=` - Direct DOM manipulation
- `document\.write` - Legacy injection vector
- `\$\{.*\}.*<` or `` `<.*\$\{ `` - Template literal HTML injection

#### Severity: CRITICAL if user input flows in, MEDIUM if static/trusted content

---

### 2. URL / Protocol Injection

#### What to Look For:
```jsx
// VULNERABLE - User-controlled href can inject javascript: protocol
<a href={userProvidedUrl}>Click here</a>

// VULNERABLE - User-controlled src
<img src={userProvidedUrl} />
<iframe src={userProvidedUrl} />

// VULNERABLE - window.location with user input
window.location.href = params.get('redirect');

// SAFE - Validate URL protocol
const safeUrl = url.startsWith('https://') || url.startsWith('http://') ? url : '#';
<a href={safeUrl}>Click here</a>

// SAFE - Use URL constructor for validation
try {
  const parsed = new URL(userUrl);
  if (!['http:', 'https:'].includes(parsed.protocol)) throw new Error();
} catch { /* invalid */ }
```

#### Grep Patterns:
- `href=\{` where value is dynamic (check for `javascript:` protocol risk)
- `src=\{` where value is user-controlled
- `window\.location\s*=` or `window\.location\.href\s*=`
- `window\.open\(` with dynamic URL

#### Severity: HIGH

---

### 3. Sensitive Data Exposure

#### What to Look For:
```jsx
// VULNERABLE - API keys in source code
const API_KEY = 'sk_live_abc123xyz';
const SECRET = 'my-super-secret-key';

// VULNERABLE - Tokens in localStorage (accessible via XSS)
localStorage.setItem('auth_token', token);
localStorage.setItem('session', JSON.stringify(userData));

// VULNERABLE - console.log with sensitive data in production
console.log('User data:', userData);
console.log('Token:', authToken);

// SAFE - Use environment variables (still exposed in bundle, but not in source)
const API_KEY = import.meta.env.VITE_API_KEY;

// SAFE - Use httpOnly cookies for auth tokens (not accessible via JS)
// Set by server: Set-Cookie: token=xyz; HttpOnly; Secure; SameSite=Strict
```

#### Grep Patterns:
- `API_KEY\s*=\s*["']` - Hardcoded API keys
- `SECRET\s*=\s*["']` - Hardcoded secrets
- `PASSWORD\s*=\s*["']` - Hardcoded passwords
- `TOKEN\s*=\s*["']` - Hardcoded tokens
- `localStorage\.setItem.*token` - Token in localStorage
- `sessionStorage\.setItem.*token` - Token in sessionStorage
- `console\.(log|debug|info|warn)` - Console output in production code

#### Files to Check:
- `.env` files committed to repo (should be in .gitignore)
- `.env.example` or `.env.sample` with real values
- Source files with hardcoded credentials
- Build config files with embedded secrets

#### Severity: CRITICAL for hardcoded secrets, HIGH for localStorage tokens, MEDIUM for console.log

---

### 4. Third-Party Dependency Risks

#### What to Check:
```bash
# Check for known vulnerabilities
npm audit

# Check for outdated packages
npm outdated

# Check specific package for issues
npm audit --package-lock-only
```

#### What to Look For:
```html
<!-- VULNERABLE - External scripts without integrity -->
<script src="https://cdn.example.com/lib.js"></script>

<!-- SAFE - Subresource Integrity -->
<script src="https://cdn.example.com/lib.js"
        integrity="sha384-abc123..."
        crossorigin="anonymous"></script>
```

#### Grep Patterns:
- `<script.*src=["']http` - External script loading
- `<link.*href=["']http` - External stylesheet loading
- Check `package.json` for known problematic packages

#### Severity: HIGH for known CVEs, MEDIUM for outdated deps

---

### 5. Client-Side Code Injection

#### What to Look For:
```jsx
// VULNERABLE - eval with any input
eval(userCode);

// VULNERABLE - new Function with dynamic code
const fn = new Function(userInput);

// VULNERABLE - setTimeout/setInterval with string argument
setTimeout("alert('xss')", 1000);
setTimeout(userInput, 1000);

// SAFE - setTimeout with function reference
setTimeout(() => doSomething(), 1000);
```

#### Grep Patterns:
- `\beval\(` - Direct eval usage
- `new\s+Function\(` - Dynamic function creation
- `setTimeout\(\s*["']` or `setTimeout\(\s*[^(]` - setTimeout with string
- `setInterval\(\s*["']` - setInterval with string

#### Severity: CRITICAL

---

### 6. Cross-Origin Communication

#### What to Look For:
```jsx
// VULNERABLE - postMessage without origin check
window.addEventListener('message', (event) => {
  // No origin validation!
  processData(event.data);
});

// VULNERABLE - postMessage to wildcard
window.parent.postMessage(sensitiveData, '*');

// SAFE - Validate origin
window.addEventListener('message', (event) => {
  if (event.origin !== 'https://trusted-domain.com') return;
  processData(event.data);
});

// SAFE - Specific target origin
window.parent.postMessage(data, 'https://trusted-domain.com');
```

#### Grep Patterns:
- `addEventListener.*["']message["']` then check for `event.origin` validation
- `postMessage\(.*,\s*["']\*["']` - Wildcard target origin

#### Severity: HIGH

---

### 7. CSRF and Authentication

#### What to Check:

**For Inertia.js apps:**
- CSRF tokens are included automatically via Rails middleware
- Verify `X-CSRF-Token` header in requests

**For SPA apps:**
- Check how auth tokens are sent (cookie vs Authorization header)
- Verify `credentials: 'include'` or `withCredentials: true` on cross-origin requests
- Check for `SameSite` cookie attributes

```jsx
// CHECK - Cross-origin requests with credentials
fetch('https://api.example.com/data', {
  credentials: 'include',  // Sends cookies cross-origin
});

axios.get('https://api.example.com/data', {
  withCredentials: true,
});
```

#### Grep Patterns:
- `credentials:\s*["']include["']` - Cross-origin cookie sending
- `withCredentials:\s*true` - Axios cross-origin cookies
- Check for CSRF token handling in request interceptors

#### Severity: HIGH for missing CSRF, MEDIUM for cross-origin credential issues

---

### 8. Content Security Policy (CSP)

#### What to Check:
- Does the server send CSP headers?
- Are `unsafe-inline` or `unsafe-eval` used?
- Is `script-src` properly configured?

```html
<!-- Check meta tag CSP -->
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'">
```

#### Files to Check:
- `index.html` or main HTML template for meta CSP
- Server config for CSP headers
- Vite/Webpack config for CSP nonce support

#### Severity: MEDIUM for missing CSP, LOW for overly permissive CSP

---

## Security Severity Levels

### CRITICAL (Fix Immediately)
- XSS via `dangerouslySetInnerHTML` with unsanitized user input
- Hardcoded API keys or secrets in source code
- `eval()` or `new Function()` with user input
- Authentication bypass
- Sensitive data in localStorage accessible via XSS

### HIGH (Fix This Week)
- URL/protocol injection in `href`/`src`
- Missing origin validation on `postMessage`
- Cross-origin requests with credentials
- npm audit critical/high vulnerabilities
- Missing CSRF protection

### MEDIUM (Fix This Sprint)
- `console.log` with sensitive data in production
- Auth tokens in localStorage (vs httpOnly cookies)
- External scripts without SRI
- Missing Content Security Policy
- Outdated dependencies (no known CVEs)

### LOW (Fix When Convenient)
- Overly permissive CSP (e.g., `unsafe-inline` for styles)
- Missing security headers (non-critical)
- Verbose error messages in production
- Source maps deployed to production
