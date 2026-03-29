# React Application Audit Report

Use this structure for the generated report. Fill each section with actual findings. Omit sections with no findings.

## Sections (in order)

### 1. Executive Summary
- Overall Health Score: X/100 (90-100 Excellent, 80-89 Good, 70-79 Fair, 60-69 Needs Attention, <60 Critical)
- Summary table: severity (Critical/High/Medium/Low) x count
- One-paragraph assessment

### 2. Audit Scope
- Project stats: total files, LOC, components, hooks, test files, dependencies
- Stack detected: language, CSS approach, linting, test framework, meta-framework

### 3. Coding Standards
- Score X/100, issues table by category
- Top issues with file:line references and severity

### 4. Component Architecture
- Complexity metrics table (lines/component, useState/useEffect counts, props count) with averages, max, and targets
- Patterns assessment: custom hooks, composition, separation of concerns, type safety

### 5. Accessibility Assessment
- Severity x count table
- Issues with file:line, impact description, WCAG reference, and fix recommendation

### 6. Testing & Coverage
- Coverage percentage (or N/A), test-to-component ratio
- Coverage table by type (pages, components, hooks, utils)
- Coverage gaps

### 7. Security Assessment
- Severity x count table
- Issues with file:line, risk level, impact, and fix recommendation

### 8. Performance Analysis
- Bundle/loading: code splitting, lazy loading, heavy deps, chunking
- Rendering: memo usage, inline functions, unnecessary re-renders
- Identified bottlenecks with file references

### 9. Error Handling & Resilience
- Coverage table: error boundaries, loading states, empty states, network errors, form validation (Yes/No/Partial)
- Issues with file references

### 10. Dependency Analysis
- npm audit results (if available)
- Heavy/outdated dependencies with alternatives

### 11. Positive Findings
- What's working well (list 3-5 strengths)

### 12. Prioritized Recommendations
- Immediate (fix this week): critical items
- Short-term (fix this sprint): high items
- Long-term (fix this quarter): medium items

### 13. Conclusion
- Overall assessment paragraph
- 3 key takeaways
- Final score and deployment recommendation

### Appendix
- Methodology: tools used, standards applied, files analyzed
- References: [WCAG 2.1](https://www.w3.org/TR/WCAG21/), [OWASP Top 10](https://owasp.org/Top10/), [React Documentation](https://react.dev), [WAI-ARIA APG](https://www.w3.org/WAI/ARIA/apg/)
- Audit date and skill version
