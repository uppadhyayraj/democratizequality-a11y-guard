# analyze

Analyze historical accessibility patterns and trends from your knowledge base.

## Syntax

```bash
a11y-guard analyze [paths...] [options]
```

## Description

The `analyze` command provides insights into your team's accessibility patterns by querying the knowledge base. It shows violation trends over time, most common issues, successful fix patterns, and areas needing attention.

This command helps teams understand:
- Which accessibility issues occur most frequently
- How fix success rates have improved over time
- Which files or components have the most issues
- What patterns lead to successful fixes
- Team-specific accessibility challenges

When file paths are provided, the analysis focuses on specific files or directories. Without paths, it analyzes the entire project.

## Options

The `analyze` command currently takes no options. When file paths are provided as arguments, the analysis focuses on those specific files. Without paths, it analyzes the entire project.

## Examples

### Basic Analysis

```bash
a11y-guard analyze
```

**Actual Output:**
```
⠴ Analyzing accessibility patterns...
🤖 AI-Powered Analysis
Using: GitHub Copilot (Model: claude-sonnet-4.5)

✔ Analysis completed

📊 Accessibility Analysis:

The knowledge base currently shows **no accessibility violations** in your codebase. This could mean:

1. **No violations exist** - Your codebase has excellent accessibility coverage
2. **Violations haven't been scanned yet** - The accessibility testing tools may not have run
3. **Knowledge base not populated** - Violation data hasn't been indexed yet

To populate violation data, you typically need to run accessibility testing tools like:
- `axe-core` or `@axe-core/react` 
- `eslint-plugin-jsx-a11y`
- Lighthouse accessibility audits
- WAVE or similar scanners

Would you like me to help set up accessibility testing or scan the codebase for violations?

🔧 Fix Recommendations:

## Common WCAG Fix Recommendations

### Missing Alt Text
<!-- ❌ Bad -->
<img src="logo.png">

<!-- ✅ Good -->
<img src="logo.png" alt="Company logo">
<img src="decorative.png" alt=""> <!-- Empty for decorative -->

### Missing Labels
<!-- ❌ Bad -->
<input type="text" placeholder="Email">

<!-- ✅ Good -->
<label for="email">Email</label>
<input id="email" type="text">
<!-- OR -->
<input type="text" aria-label="Email">

### Color Contrast
- Ensure **4.5:1** ratio for normal text
- Ensure **3:1** ratio for large text (18pt+ or 14pt+ bold)

### Keyboard Access
<!-- ❌ Bad: div acting as button -->
<div onclick="submit()">Submit</div>

<!-- ✅ Good: Use native elements -->
<button onclick="submit()">Submit</button>

📊 Agent Token Usage

History Agent:
Input tokens:  11092
Output tokens: 164
Cache tokens:  10398
Total tokens:  11256

Fix Agent:
Input tokens:  10768
Output tokens: 520
Cache tokens:  5215
Total tokens:  11288

💡 Tip: Use "a11y-guard ask <question>" to learn more about WCAG guidelines.
```

### Analyze Specific File

```bash
a11y-guard analyze src/components/LoginForm.tsx
```

**Actual Output:**
```
⠏ Analyzing accessibility patterns...
🤖 AI-Powered Analysis
Using: GitHub Copilot (Model: claude-sonnet-4.5)

✔ Analysis completed

📊 Accessibility Analysis:

## Accessibility Pattern Analysis: LoginForm.tsx

**Violation Database:** No historical violations found in the knowledge base for this file yet.

**Code Analysis - Identified Patterns:**

1. **Missing Form Labels (Lines 19-26, 31-38, 42-44)**
   - 3 instances of inputs lacking proper `<label>` associations
   - Violates WCAG 1.3.1 (Info and Relationships) & 3.3.2 (Labels or Instructions) - Level A
   - Pattern: Using placeholders or plain `<div>` text instead of semantic labels

2. **Common Anti-patterns:**
   - Placeholder-as-label (line 21-22): Using `placeholder="Email"` without label
   - Visual text without association (line 30): `<div>Password</div>` not connected to input
   - Orphaned label (line 42): `<label>` without `htmlFor` or wrapping structure

**Impact:** Screen reader users cannot identify input purposes, form completion rates likely reduced.

🔧 Fix Recommendations:

## Accessibility Issues Found in LoginForm.tsx

#### 1. **Email Input Missing Label** (Lines 19-25)
// ❌ Before
<input 
  type="email"
  placeholder="Email"
  className="input"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
/>

// ✅ Fix - Add proper label association
<label htmlFor="email-input">Email</label>
<input 
  id="email-input"
  type="email"
  placeholder="Email"
  className="input"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
/>

#### 2. **Password Input with Non-Associated Label** (Lines 29-38)
// ❌ Before - div is not a label
<div>Password</div>
<input type="password" ... />

// ✅ Fix - Use proper label with htmlFor
<label htmlFor="password-input">Password</label>
<input 
  id="password-input"
  type="password"
  placeholder="Enter password"
  className="input"
  value={password}
  onChange={(e) => setPassword(e.target.value)}
/>

#### 3. **Checkbox Without Label Association** (Lines 41-44)
// ❌ Before
<label>Remember me</label>
<input type="checkbox" />

// ✅ Fix
<label htmlFor="remember-checkbox">Remember me</label>
<input id="remember-checkbox" type="checkbox" />

📊 Agent Token Usage

History Agent:
Input tokens:  11674
Output tokens: 249
Cache tokens:  10389
Total tokens:  11923

Fix Agent:
Input tokens:  11550
Output tokens: 602
Cache tokens:  10355
Total tokens:  12152

💡 Tip: Use "a11y-guard ask <question>" to learn more about WCAG guidelines.
```



## How It Works

### Knowledge Base Queries

```typescript
// Analysis engine
async function analyze(options) {
  const kb = await openKnowledgeBase();
  
  // Query violations
  const violations = await kb.query(`
    SELECT 
      rule,
      file,
      severity,
      status,
      created_at,
      fixed_at
    FROM violations
    WHERE created_at > date('now', '-${options.period}')
  `);
  
  // Query fixes
  const fixes = await kb.query(`
    SELECT 
      pattern,
      success,
      confidence,
      applied_at
    FROM fixes
    WHERE applied_at > date('now', '-${options.period}')
  `);
  
  // Aggregate and analyze
  return {
    summary: calculateSummary(violations, fixes),
    topViolations: groupAndRank(violations, options.groupBy),
    patterns: analyzePatterns(fixes),
    trends: calculateTrends(violations, fixes),
    recommendations: generateRecommendations(violations, fixes)
  };
}
```

### Metrics Calculation

```typescript
// Key metrics
function calculateSummary(violations, fixes) {
  return {
    totalViolations: violations.length,
    violationsFixed: violations.filter(v => v.status === 'fixed').length,
    activeViolations: violations.filter(v => v.status === 'active').length,
    fixSuccessRate: fixes.filter(f => f.success).length / fixes.length,
    averageTimeToFix: calculateAverageTime(violations)
  };
}

// Trend analysis
function calculateTrends(violations, fixes) {
  const weeks = groupByWeek(violations);
  return weeks.map(week => ({
    week: week.number,
    violations: week.violations.length,
    fixed: week.violations.filter(v => v.status === 'fixed').length,
    fixRate: week.violations.filter(v => v.status === 'fixed').length / week.violations.length
  }));
}
```

## Common Use Cases

### 1. Sprint Retrospective

**Scenario:** Review accessibility progress during sprint review.

```bash
# Generate sprint report
a11y-guard analyze > sprint-a11y-report.txt

# Present in standup
cat sprint-a11y-report.txt
```

### 2. Identify Problem Areas

**Scenario:** Find which components need the most attention.

```bash
# Get analysis of all files
a11y-guard analyze

# Dive into specific file
a11y-guard analyze src/components/ImageGallery.tsx

# Create action items
a11y-guard fix src/components/ImageGallery.tsx
```

### 3. Track Learning Effectiveness

**Scenario:** See if team is learning from past mistakes.

```bash
# Check patterns and trends
a11y-guard analyze

# If fix rate improving → team learning ✅
# If same issues recurring → need training
```

### 4. Basic Reporting

**Scenario:** Generate report for accessibility review.

```bash
# Full analysis
a11y-guard analyze > compliance-report.txt

# Include in documentation
cp compliance-report.txt docs/accessibility/
```

## Troubleshooting

### Issue: "Knowledge base is empty"

**Cause:** No scans or fixes have been recorded yet.

**Solution:**
```bash
# Run initial scan
a11y-guard check src/

# Or learn from git history
a11y-guard learn --full

# Then analyze
a11y-guard analyze
```

### Issue: Analysis shows no trends

**Cause:** Insufficient historical data (need 2+ weeks).

**Solution:**
```bash
# If recent, wait or learn from git history
a11y-guard learn --full --commits 1000

# This backfills historical data
```

### Issue: File analysis says "File not found"

**Cause:** File was renamed or deleted.

**Solution:**
```bash
# The KB has old filename, but analysis shows historical data
# Find renamed files
git log --follow --name-status -- oldname.tsx
```

### Issue: Want to export data

**Cause:** Need data in different format.

**Solution:**
```bash
# Export to file
a11y-guard analyze > analysis-report.txt
```

## Related Commands

- [check](./check.md) - Scan for violations (populates the data analyzed here)
- [fix](./fix.md) - Apply fixes (success tracked in analysis)
- [learn](./learn.md) - Build historical data from git
- [ask](./ask.md) - Query AI about patterns in your data

## Additional Notes

### Database Schema

The analysis queries these tables:

```sql
-- Violations table
CREATE TABLE violations (
  id INTEGER PRIMARY KEY,
  file TEXT,
  line INTEGER,
  rule TEXT,
  severity TEXT,
  status TEXT,  -- 'active' or 'fixed'
  created_at TIMESTAMP,
  fixed_at TIMESTAMP
);

-- Fixes table
CREATE TABLE fixes (
  id INTEGER PRIMARY KEY,
  violation_id INTEGER,
  pattern TEXT,
  confidence REAL,
  success BOOLEAN,
  applied_at TIMESTAMP
);

-- Scan sessions
CREATE TABLE scan_sessions (
  id TEXT PRIMARY KEY,
  mode TEXT,
  files_scanned INTEGER,
  violations_found INTEGER,
  timestamp TIMESTAMP
);
```

### Performance

- Fast for typical projects (< 1 second)
- Database indexed on common query fields
- Large projects (10k+ violations) may take 2-3 seconds


### Privacy

All analysis is local:
- No data sent to external services
- Database stays in `.a11y-guard/knowledge.db`
- Safe to share anonymized JSON exports

### Continuous Monitoring

Set up automated analysis:

```bash
# Daily cron job
0 9 * * * cd /path/to/project && a11y-guard analyze > daily-report.txt

# Weekly summary
0 9 * * 1 cd /path/to/project && a11y-guard analyze | \
  mail -s "Weekly A11y Report" team@company.com
```
