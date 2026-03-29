# React Security Audit Checklist

Grep patterns and severity classifications for React security auditing.

## 1. XSS Vectors

Grep patterns:
- `dangerouslySetInnerHTML` — every usage needs review; CRITICAL if user input flows in, MEDIUM if static
- `\.innerHTML\s*=` — direct DOM manipulation
- `document\.write` — legacy injection vector
- `` \$\{.*\}.*< `` or `` `<.*\$\{ `` — template literal HTML injection

## 2. URL / Protocol Injection

Grep patterns:
- `href=\{` with dynamic value — check for `javascript:` protocol risk
- `src=\{` with user-controlled value
- `window\.location\s*=` or `window\.location\.href\s*=`
- `window\.open\(` with dynamic URL

Severity: HIGH

## 3. Sensitive Data Exposure

Grep patterns:
- `API_KEY\s*=\s*["']` — hardcoded API keys
- `SECRET\s*=\s*["']` — hardcoded secrets
- `PASSWORD\s*=\s*["']` — hardcoded passwords
- `TOKEN\s*=\s*["']` — hardcoded tokens
- `localStorage\.setItem.*token` — token in localStorage
- `sessionStorage\.setItem.*token` — token in sessionStorage
- `console\.(log|debug|info|warn)` — console output in production code

Files to check:
- `.env` files committed to repo (should be in .gitignore)
- `.env.example` with real values

Severity: CRITICAL for hardcoded secrets, HIGH for localStorage tokens, MEDIUM for console.log

## 4. Client-Side Code Injection

Grep patterns:
- `\beval\(` — direct eval usage
- `new\s+Function\(` — dynamic function creation
- `setTimeout\(\s*["']` or `setTimeout\(\s*[^(]` — setTimeout with string
- `setInterval\(\s*["']` — setInterval with string

Severity: CRITICAL

## 5. Cross-Origin Communication

Grep patterns:
- `addEventListener.*["']message["']` — check for `event.origin` validation
- `postMessage\(.*,\s*["']\*["']` — wildcard target origin

Severity: HIGH

## 6. CSRF & Authentication

Check:
- How auth tokens are stored and sent
- For Inertia.js: verify CSRF token handling via Rails middleware
- `credentials:\s*["']include["']` — cross-origin cookie sending
- `withCredentials:\s*true` — Axios cross-origin cookies

Severity: HIGH for missing CSRF, MEDIUM for cross-origin credential issues

## 7. Third-Party Dependencies

- Run `npm audit` if package-lock.json exists
- `<script.*src=["']http` — external script without SRI
- Check `package.json` for known problematic packages

Severity: HIGH for known CVEs, MEDIUM for outdated deps

## Severity Summary

| Level | Examples |
|-------|---------|
| CRITICAL | XSS with user input, hardcoded secrets, eval with user input, auth bypass |
| HIGH | URL injection, missing origin validation, cross-origin credentials, npm CVEs, missing CSRF |
| MEDIUM | console.log with sensitive data, localStorage tokens, external scripts without SRI, outdated deps |
| LOW | Overly permissive CSP, verbose error messages, source maps in production |
