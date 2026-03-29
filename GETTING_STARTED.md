# Getting Started with React Audit Skill

## Quick Start

The React Audit skill is now installed and ready to use! This guide will help you run your first audit.

## Installation Verification

Your skill is installed at:
```
~/.claude/skills/react-audit/
```

Files included:
- `SKILL.md` - Main skill definition
- `coding-standards-reference.md` - React best practices
- `security-checklist.md` - React security vulnerability checks
- `a11y-checklist.md` - Accessibility audit checklist
- `report-template.md` - Report structure template
- `README.md` - Comprehensive documentation

## Running Your First Audit

### Step 1: Navigate to Your React Project

```bash
cd /path/to/your/react/project
```

### Step 2: Run the Audit

In Claude Code, simply type:

```
/react-audit
```

Or with a custom filename:

```
/react-audit my-project-audit
```

### Step 3: Review the Results

The skill will:
1. Scan your entire React project (respecting .gitignore)
2. Auto-detect your framework, build tool, and CSS approach
3. Analyze coding standards and React patterns
4. Audit accessibility against WCAG 2.1 AA
5. Scan for security vulnerabilities
6. Review component architecture and performance
7. Generate a comprehensive Markdown report

## What to Expect

### Audit Duration
- Small projects (<10k LOC): 2-3 minutes
- Medium projects (10k-50k LOC): 5-10 minutes
- Large projects (>50k LOC): 10-20 minutes

### Report Output
You'll get a Markdown file: `react-audit-report-YYYY-MM-DD.md`

### Report Sections
1. Executive Summary with overall score
2. Detailed findings by category
3. Accessibility issues with WCAG references
4. Security vulnerabilities with severity
5. Performance bottlenecks
6. Prioritized recommendations
7. Action plan with timeline

## Example Usage Scenarios

### Scenario 1: Pre-Deployment Audit
```
# Before deploying to production
cd ~/projects/my-react-app
/react-audit pre-deployment-audit

# Review critical issues
# Fix security and a11y vulnerabilities
# Deploy with confidence
```

### Scenario 2: Accessibility Review
```
# Review a11y compliance before launch
/react-audit a11y-review

# Focus on the Accessibility Assessment section
# Fix critical keyboard and screen reader issues
# Run Lighthouse for runtime verification
```

### Scenario 3: Technical Debt Assessment
```
# Quarterly technical debt review
/react-audit q1-2026-tech-debt

# Track improvements over time
# Compare with previous quarter
# Plan refactoring sprints
```

## Understanding the Score

The audit generates an overall score (0-100):

- **90-100**: Excellent - Production ready
- **80-89**: Good - Minor improvements needed
- **70-79**: Fair - Some issues to address
- **60-69**: Needs Attention - Multiple issues
- **<60**: Critical - Significant issues

## Priority Levels

Issues are categorized by severity:

🔴 **Critical** (Fix Immediately)
- Security vulnerabilities
- Accessibility blockers
- Data exposure risks

🟠 **High** (Fix This Week)
- Major a11y gaps
- Missing error handling
- Performance bottlenecks

🟡 **Medium** (Fix This Sprint)
- Code quality issues
- Missing tests
- Technical debt

🟢 **Low** (Nice to Have)
- Style inconsistencies
- Minor optimizations
- Documentation

## Customizing the Audit

### Modify Coding Standards

Edit: `~/.claude/skills/react-audit/coding-standards-reference.md`

Add your team's specific React conventions or patterns.

### Update Security Checks

Edit: `~/.claude/skills/react-audit/security-checklist.md`

Add industry-specific security requirements.

### Adjust Accessibility Checks

Edit: `~/.claude/skills/react-audit/a11y-checklist.md`

Customize for your compliance requirements (AA vs AAA).

### Change Report Format

Edit: `~/.claude/skills/react-audit/report-template.md`

Customize the section structure and add branding.

## Tips for Best Results

1. **Run in Clean State**: Commit or stash changes before audit
2. **Update Dependencies**: Run `npm install` first
3. **Check .gitignore**: Ensure it excludes `node_modules/`, `dist/`, `build/`
4. **Review Regularly**: Run monthly or before major releases
5. **Track Progress**: Keep historical reports to track improvements
6. **Share with Team**: Use the report for team discussions
7. **Act on Findings**: Create tickets for high-priority issues

## Troubleshooting

### "Command not found: /react-audit"
- Restart Claude Code or VSCode
- Check skill is in: `~/.claude/skills/react-audit/SKILL.md`

### Audit is slow
- Check .gitignore includes: `node_modules/`, `dist/`, `build/`
- Large projects take longer - be patient

### Permission errors
- Ensure Claude Code can read project files
- Check file permissions

## Next Steps

1. Run your first audit: `/react-audit`
2. Review the generated report
3. Fix critical issues immediately
4. Create tickets for other issues
5. Schedule regular audits

## Support

- **Documentation**: See `README.md` in skill directory
- **Report Issues**: https://github.com/tannermares/react-audit-claude-skill/issues
- **Get Help**: Type `/help` in Claude Code

---

**Ready to audit your React app?** Just type `/react-audit` and let Claude do the work!
