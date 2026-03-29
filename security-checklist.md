# React Security Audit Checklist

Grep patterns and severity classifications for React security auditing.

## 1. XSS Vectors

> **References:**
> - [OWASP: XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
> - [CWE-79: Improper Neutralization of Input](https://cwe.mitre.org/data/definitions/79.html)
> - [React: dangerouslySetInnerHTML](https://react.dev/reference/react-dom/components/common#dangerously-setting-the-inner-html)
> - [OWASP: DOM-based XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)

Grep patterns:
- `dangerouslySetInnerHTML` — every usage needs review; CRITICAL if user input flows in, MEDIUM if static. Check for `DOMPurify.sanitize()` nearby as mitigation.
- `\.innerHTML\s*=` — dangerous sink (DOM-based XSS)
- `\.outerHTML\s*=` — dangerous sink (same risk as innerHTML)
- `\.insertAdjacentHTML\(` — parses HTML like innerHTML
- `document\.write` or `document\.writeln` — legacy injection vectors
- `` \$\{.*\}.*< `` or `` `<.*\$\{ `` — template literal HTML injection
- `\.on\w+\s*=` — event handler attribute assignment as dangerous sink

Safe alternative: `.textContent =` (does not parse HTML)

## 2. URL / Protocol Injection

> **References:**
> - [CWE-601: URL Redirection to Untrusted Site](https://cwe.mitre.org/data/definitions/601.html)
> - [OWASP: Unvalidated Redirects and Forwards](https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html)

Grep patterns:
- `href=\{` with dynamic value — check for `javascript:` and `data:` protocol risk
- `src=\{` with user-controlled value
- `window\.location\s*=` or `window\.location\.href\s*=`
- `location\.replace\(` or `location\.assign\(` — redirect vectors
- `window\.open\(` with dynamic URL
- `location\.(hash|search|pathname)` — user-controlled URL sources flowing into sinks (DOM-based XSS)

Dynamic URLs should be validated against an allowlist of approved domains/paths.

Severity: HIGH

## 3. Sensitive Data Exposure

> **References:**
> - [OWASP A02:2021 Cryptographic Failures](https://owasp.org/Top10/2021/A02_2021-Cryptographic_Failures/)
> - [CWE-798: Hardcoded Credentials](https://cwe.mitre.org/data/definitions/798.html)
> - [CWE-532: Info Exposure Through Log Files](https://cwe.mitre.org/data/definitions/532.html)
> - [OWASP: Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)

Grep patterns:
- `API_KEY\s*=\s*["']` — hardcoded API keys
- `SECRET\s*=\s*["']` — hardcoded secrets
- `PASSWORD\s*=\s*["']` — hardcoded passwords
- `TOKEN\s*=\s*["']` — hardcoded tokens
- `PRIVATE.KEY|BEGIN.*PRIVATE` — private keys in source
- `DATABASE_URL\s*=\s*["']` — hardcoded connection strings
- `REACT_APP_.*SECRET|REACT_APP_.*KEY|NEXT_PUBLIC_.*SECRET|VITE_.*SECRET` — secrets in client-exposed env vars (these are bundled into client code)
- `localStorage\.setItem.*token` — token in localStorage (vulnerable to XSS)
- `sessionStorage\.setItem.*token` — token in sessionStorage
- `document\.cookie` — direct cookie access from JavaScript
- `console\.(log|debug|info|warn)` — console output in production code

Files to check:
- `.env` files committed to repo (should be in .gitignore)
- `.env.example` with real values
- `sourceMappingURL` in production bundles — exposes source code

Severity: CRITICAL for hardcoded secrets/private keys, HIGH for localStorage tokens, MEDIUM for console.log

## 4. Client-Side Code Injection

> **References:**
> - [CWE-95: Eval Injection](https://cwe.mitre.org/data/definitions/95.html)
> - [MDN: Never use eval()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval#never_use_direct_eval!)

Grep patterns:
- `\beval\(` — direct eval usage (use `JSON.parse()` for JSON data instead)
- `new\s+Function\(` — dynamic function creation
- `setTimeout\(\s*["']` or `setTimeout\(\s*[^(]` — setTimeout with string argument
- `setInterval\(\s*["']` — setInterval with string argument
- `window\[` with dynamic property — can execute arbitrary code via property access
- `import\(.*\$\{` or `import\(.*\+` — dynamic import with user-controlled path

Severity: CRITICAL

## 5. Cross-Origin Communication

> **References:**
> - [MDN: postMessage Security Concerns](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage#security_concerns)
> - [CWE-346: Origin Validation Error](https://cwe.mitre.org/data/definitions/346.html)

Grep patterns:
- `addEventListener.*["']message["']` — check for `event.origin` validation nearby; absence is HIGH
- `postMessage\(.*,\s*["']\*["']` — wildcard target origin
- `Access-Control-Allow-Origin.*\*` — wildcard CORS (allows any origin)
- `cors\(\{` — CORS middleware config; check for overly permissive origins

Severity: HIGH

## 6. CSRF & Authentication

> **References:**
> - [OWASP: CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
> - [OWASP: Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
> - [OWASP: Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

Grep patterns:
- `X-CSRF-Token|X-XSRF-Token|csrf.token|_csrf` — verify CSRF token handling exists
- `credentials:\s*["']include["']` — cross-origin cookie sending
- `withCredentials:\s*true` — Axios cross-origin cookies
- `SameSite` — check cookie attribute is set to `Lax` or `Strict` (defense-in-depth for CSRF)
- `httpOnly|HttpOnly` — session cookies must have HttpOnly flag (prevents JS access)
- `[Ss]ecure` — cookies with tokens must have Secure flag (HTTPS only)

Check:
- How auth tokens are stored and sent (session tokens should be in HttpOnly cookies, not localStorage)
- For Inertia.js: verify CSRF token handling via Rails middleware
- State-changing operations should not use GET requests

Severity: HIGH for missing CSRF or missing HttpOnly on session cookies, MEDIUM for cross-origin credential issues

## 7. Third-Party Dependencies

> **References:**
> - [OWASP A06:2021 Vulnerable and Outdated Components](https://owasp.org/Top10/2021/A06_2021-Vulnerable_and_Outdated_Components/)
> - [MDN: Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)
> - [OWASP: Third Party JavaScript Management](https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_JavaScript_Management_Cheat_Sheet.html)

- Run `npm audit` (or `yarn audit`) if lock file exists
- `<script` with external `src` but no `integrity=` attribute — missing SRI
- `integrity=` without `crossorigin` attribute — SRI requires CORS for cross-origin scripts
- Check `package.json` for known problematic packages
- `<iframe(?![^>]*sandbox=)` — third-party content without sandbox
- `googletagmanager|gtm\.js` — tag managers can introduce XSS via their GUI; flag for review

Severity: HIGH for known CVEs, MEDIUM for outdated deps and missing SRI

## Severity Summary

| Level | Examples |
|-------|---------|
| CRITICAL | XSS with user input, hardcoded secrets, eval with user input, auth bypass |
| HIGH | URL injection, missing origin validation, cross-origin credentials, npm CVEs, missing CSRF |
| MEDIUM | console.log with sensitive data, localStorage tokens, external scripts without SRI, outdated deps |
| LOW | Overly permissive CSP, verbose error messages, source maps in production |
