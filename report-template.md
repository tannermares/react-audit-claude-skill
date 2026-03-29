# React Application Audit Report

**Project**: [PROJECT_NAME]
**Audit Date**: [DATE]
**React Version**: [REACT_VERSION]
**Build Tool**: [BUILD_TOOL]
**Framework**: [FRAMEWORK]
**Auditor**: Claude Code

---

# Executive Summary

## Overall Health Score: [SCORE]/100

| Priority | Count |
|----------|-------|
| 🔴 Critical | [N] |
| 🟠 High | [N] |
| 🟡 Medium | [N] |
| 🟢 Low | [N] |

**Status**: [EXCELLENT/GOOD/FAIR/NEEDS_ATTENTION/CRITICAL]

---

# Audit Scope

## Project Statistics

- **Total Files Analyzed**: [N]
- **Total Lines of Code**: [N]
- **Pages**: [N]
- **Components**: [N]
- **Custom Hooks**: [N]
- **Test Files**: [N]
- **Dependencies**: [N] production / [N] dev

## Stack Detected

- **Language**: [JavaScript/TypeScript]
- **CSS Approach**: [Tailwind/CSS Modules/Styled Components/etc.]
- **Linting**: [ESLint/Biome/None]
- **Test Framework**: [Jest/Vitest/None]
- **Meta-Framework**: [Inertia/Next.js/Remix/None]

---

# Coding Standards Assessment

## Score: [X]/100

### Issues Found

| Category | Count | Severity |
|----------|-------|----------|
| [Category 1] | [N] | [Level] |
| [Category 2] | [N] | [Level] |
| [Category 3] | [N] | [Level] |

---

# Coding Standards - Top Issues

## 1. [Issue Title]
**File**: [filename.jsx:line]
**Severity**: [Critical/High/Medium/Low]
**Description**: [Brief description]

## 2. [Issue Title]
**File**: [filename.jsx:line]
**Severity**: [Critical/High/Medium/Low]
**Description**: [Brief description]

## 3. [Issue Title]
**File**: [filename.jsx:line]
**Severity**: [Critical/High/Medium/Low]
**Description**: [Brief description]

---

# Component Architecture

## Complexity Metrics

| Metric | Average | Max | Target |
|--------|---------|-----|--------|
| Lines per component | [N] | [N] | <150 |
| useState calls | [N] | [N] | <5 |
| useEffect calls | [N] | [N] | <3 |
| Props per component | [N] | [N] | <8 |

## Patterns Assessment

| Pattern | Status | Notes |
|---------|--------|-------|
| Custom Hooks | [Well Used/Missing/Overused] | [Notes] |
| Component Composition | [Well Used/Missing/Overused] | [Notes] |
| Separation of Concerns | [Well Used/Missing/Overused] | [Notes] |
| Type Safety | [Well Used/Missing/N/A] | [Notes] |

---

# Accessibility Assessment

## Vulnerabilities Found

| Priority | Count |
|----------|-------|
| 🔴 Critical | [N] |
| 🟠 High | [N] |
| 🟡 Medium | [N] |
| 🟢 Low | [N] |

**Overall A11y Score**: [X]/100

---

# Accessibility - Critical Issues

## 🔴 [Issue Type]
**File**: [filename.jsx:line]
**Impact**: [Description of impact on users]
**Recommendation**: [How to fix]

## 🟠 [Issue Type]
**File**: [filename.jsx:line]
**Impact**: [Description]
**Recommendation**: [How to fix]

---

# Testing & Coverage

## Overall Coverage: [X]% (or N/A if no tests)

| Component Type | Files | Tested | Coverage |
|----------------|-------|--------|----------|
| Pages | [N] | [N] | [X]% |
| Components | [N] | [N] | [X]% |
| Hooks | [N] | [N] | [X]% |
| Utils | [N] | [N] | [X]% |

**Test-to-Component Ratio**: [X:1]

## Coverage Gaps

1. **[Component/Area]**
   - Missing: [Description of what's not tested]

2. **[Component/Area]**
   - Missing: [Description of what's not tested]

---

# Security Assessment

## Vulnerabilities Found

| Priority | Count |
|----------|-------|
| 🔴 Critical | [N] |
| 🟠 High | [N] |
| 🟡 Medium | [N] |
| 🟢 Low | [N] |

**Overall Security Score**: [X]/100

---

# Security - Critical Issues

## 🔴 [Vulnerability Type]
**File**: [filename.jsx:line]
**Risk**: Critical
**Impact**: [Description of potential impact]
**Recommendation**: [How to fix]

## 🟠 [Vulnerability Type]
**File**: [filename.jsx:line]
**Risk**: High
**Recommendation**: [How to fix]

---

# Performance Analysis

## Bundle & Loading

- **Code Splitting**: [Yes/No/Partial]
- **Lazy Loading**: [N] components lazy-loaded
- **Heavy Dependencies**: [List any oversized deps]
- **Vendor Chunks**: [Description of chunking strategy]

## Rendering Performance

- **React.memo Usage**: [Appropriate/Missing/Overused]
- **Inline Functions in JSX**: [N] potential issues
- **Unnecessary Re-renders**: [N] potential issues

## Identified Bottlenecks

1. [Performance issue with file reference]
2. [Performance issue with file reference]

---

# Error Handling & Resilience

## Coverage

| Pattern | Present | Notes |
|---------|---------|-------|
| Error Boundaries | [Yes/No/Partial] | [Notes] |
| Loading States | [Yes/No/Partial] | [Notes] |
| Empty States | [Yes/No/Partial] | [Notes] |
| Network Error Handling | [Yes/No/Partial] | [Notes] |
| Form Validation | [Yes/No/Partial] | [Notes] |

## Issues

1. **[Issue]** - [filename.jsx:line]
   Impact: [Description]

2. **[Issue]** - [filename.jsx:line]
   Impact: [Description]

---

# Dependency Analysis

## npm Audit Results

| Severity | Count |
|----------|-------|
| Critical | [N] |
| High | [N] |
| Moderate | [N] |
| Low | [N] |

## Heavy Dependencies

| Package | Size | Alternative |
|---------|------|-------------|
| [package] | [size] | [suggested replacement] |

## Outdated Dependencies

| Package | Current | Latest | Risk |
|---------|---------|--------|------|
| [package] | [version] | [version] | [High/Medium/Low] |

---

# Code Complexity Metrics

| Metric | Average | Max | Target |
|--------|---------|-----|--------|
| Component Lines | [N] | [N] (file) | <150 |
| useState per Component | [N] | [N] (file) | <5 |
| useEffect per Component | [N] | [N] (file) | <3 |
| Props per Component | [N] | [N] (file) | <8 |
| JSX Nesting Depth | [N] | [N] (file) | <7 |

---

# Critical Issues Summary

## Must Fix Immediately (Critical)

1. **[Issue]** - [filename.jsx:line]
   Impact: [Description]

2. **[Issue]** - [filename.jsx:line]
   Impact: [Description]

---

# Recommendations - Immediate Actions

## Fix This Week

1. **[Action Item]**
   - Priority: Critical
   - Effort: [High/Medium/Low]
   - Impact: [High/Medium/Low]

2. **[Action Item]**
   - Priority: Critical
   - Effort: [High/Medium/Low]
   - Impact: [High/Medium/Low]

---

# Recommendations - Short Term

## Fix This Sprint (2-4 weeks)

1. **[Action Item]**
   - Priority: High
   - Effort: [High/Medium/Low]
   - Files Affected: [N]

2. **[Action Item]**
   - Priority: High
   - Effort: [High/Medium/Low]
   - Files Affected: [N]

---

# Recommendations - Long Term

## Fix This Quarter

1. **[Action Item]**
   - Priority: Medium
   - Effort: [High/Medium/Low]
   - Strategic Value: [High/Medium/Low]

2. **[Action Item]**
   - Priority: Medium
   - Effort: [High/Medium/Low]
   - Strategic Value: [High/Medium/Low]

---

# Technical Debt Estimation

## Current Technical Debt

- **Estimated Time to Fix All Issues**: [N] hours
- **Critical Issues**: [N] hours
- **High Priority**: [N] hours
- **Medium Priority**: [N] hours
- **Low Priority**: [N] hours

---

# Positive Findings

## What's Working Well

1. **[Positive aspect]**
   - [Details about what's good]

2. **[Positive aspect]**
   - [Details about what's good]

3. **[Positive aspect]**
   - [Details about what's good]

---

# Comparison to React Best Practices

| Practice | Status | Notes |
|----------|--------|-------|
| Function Components | [✓/✗/Partial] | [Notes] |
| Hooks Usage | [✓/✗/Partial] | [Notes] |
| TypeScript | [✓/✗/Partial] | [Notes] |
| Linting & Formatting | [✓/✗/Partial] | [Notes] |
| Testing | [✓/✗/Partial] | [Notes] |
| Accessibility | [✓/✗/Partial] | [Notes] |
| Code Splitting | [✓/✗/Partial] | [Notes] |
| Error Boundaries | [✓/✗/Partial] | [Notes] |
| Design Tokens | [✓/✗/Partial] | [Notes] |
| Component Composition | [✓/✗/Partial] | [Notes] |

---

# Next Steps

## Recommended Action Plan

1. **Week 1**: Fix all critical security and accessibility issues
2. **Week 2-3**: Address high-priority performance and a11y gaps
3. **Week 4-6**: Add test coverage to critical paths
4. **Month 2**: Refactor identified anti-patterns
5. **Month 3**: Set up linting, formatting, and CI checks
6. **Ongoing**: Maintain code quality standards

---

# Conclusion

## Overall Assessment

[Summary paragraph about the overall state of the React application]

## Key Takeaways

1. [Main takeaway]
2. [Main takeaway]
3. [Main takeaway]

## Final Score: [X]/100

**Recommendation**: [Proceed with deployment / Address critical issues first / Major refactoring needed]

---

# Appendix

## Audit Methodology

- Tools Used: Claude Code (Glob, Grep, Read, Bash)
- Standards Applied: React Best Practices, WCAG 2.1 AA, OWASP Client-Side Security
- Files Analyzed: [N]

## Contact

For questions about this audit:
- Generated by: Claude Code
- Date: [DATE]
- Skill Version: 1.0.0

---

**Next Audit Recommended**: [DATE + 3 months]
