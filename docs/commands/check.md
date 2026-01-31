# check

Scan files for accessibility violations using static analysis, live browser testing, or AI-powered smart scanning.

## Syntax

```bash
a11y-guard check [options]
```

## Description

The `check` command is A11yGuard's primary scanning tool. It can detect accessibility violations using three different modes:

1. **Static Mode** (default): Fast ESLint-based analysis of source code
2. **Live Mode**: Browser-based testing with axe-core for runtime violations
3. **Smart Mode**: AI-powered prediction of likely issues based on learned patterns

All scan results are automatically recorded in the knowledge base for historical analysis and pattern learning. The command can be configured to block commits or deployments when violations are detected, and can compare results against a baseline to catch regressions.

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--mode` | string | `smart` | Scanning mode: `static`, `live`, or `smart` |
| `--commit` | string | - | Scan specific commit: `staged`, `HEAD`, or commit hash |
| `--files` | string | - | Specific files to scan (space-separated paths) |
| `--url` | string | - | URL for live mode scanning (required when `--mode live`) |
| `--compare-baseline` | boolean | false | Compare results against baseline to detect regressions |
| `--block-on-regression` | boolean | false | Exit with error code if regressions detected |

## Examples

### Basic Smart Scan (Default)

```bash
a11y-guard check src/components/LoginForm.tsx
```

**Actual Output:**
```
⠏ Running smart scan with A11yGuard...
🤖 AI-Powered Analysis
Using: GitHub Copilot (Model: claude-sonnet-4.5)

⠦ Analyzing file risk with AI...Risk Analysis:

  🟢 LOW src/components/LoginForm.tsx: 30% risk

💡 AI Reasoning: LoginForm.tsx has a 30% risk score, below the 40% threshold for safe files. No files meet the high-risk criteria (
≥70%) or medium-risk threshold (≥40%) requiring scanning.
🤖 Orchestrating multi-strategy analysis...

Running static analysis...
[Static Scanner] No JavaScript/TypeScript files to scan

📊 Orchestration Summary:

  Strategies: static-scanner
  Avg Confidence: 0%
  Total Time: 0ms

✔ Smart scan completed

📊 Scan Results

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ No accessibility violations found!

Smart scan orchestrated 1 strategy, analyzed 0 files with 0% avg confidence. Found 0 violations ranked by priority.

💰 Token Usage
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Input tokens:  10844
Output tokens: 410
Cache tokens:  5215
Total tokens:  11254
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Static Scan Mode

```bash
a11y-guard check --mode static src/components/*.tsx
```

**Actual Output:**
```
⠏ Running static scan with A11yGuard...
📋 Static Scan - ESLint with jsx-a11y

Scanning 1 file:

  📄 LoginForm.tsx (src/components)

[Static Scanner] Found 1 violations in 849ms
✔ static scan completed

📊 Scan Results

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️  Found 1 accessibility issue in 1 file


📄 LoginForm.tsx
   src/components/LoginForm.tsx
   1 issue

   ❌ SERIOUS: Line# - 42
      jsx-a11y/label-has-associated-control
      A form label must be associated with a control.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ Accessibility violations detected

Found 1 accessibility violations in 1 files
Scan completed in 849ms
```

### Compare Against Baseline

```bash
a11y-guard check --compare-baseline
```

This mode compares current scan results against a stored baseline to detect regressions.

## How It Works

### Smart Scan Architecture

Smart mode uses a multi-stage AI-powered workflow to intelligently decide which files to scan:

**1. Risk Analysis Phase**

For each file, A11yGuard queries the knowledge base and calculates a risk score (0-100%):

**Risk Score Categories:**
- **80% (Unknown)**: File never scanned before - treated as high-risk
- **10-15% (Clean)**: File scanned 2+ times with zero violations
- **30-100% (Risky)**: File has violation history

**Risk Calculation Formula:**
```
baseRisk = min(totalViolations / 5, 1.0)
  - 1 violation = 20% base risk
  - 3 violations = 60% base risk  
  - 5+ violations = 100% base risk

recentRisk = min(last30DaysViolations / 3, 1.0)
  - 1 recent violation = 33% recent risk
  - 2 recent violations = 67% recent risk
  - 3+ recent violations = 100% recent risk

finalRisk = max(0.3, baseRisk * 0.4 + recentRisk * 0.6)
```

Recent violations are weighted more heavily (60%) because they indicate current problems.

**Example:**
```
LoginForm.tsx history:
- Total scans: 5
- Total violations: 3  
- Violations in last 30 days: 2

baseRisk = min(3/5, 1.0) = 0.6 (60%)
recentRisk = min(2/3, 1.0) = 0.67 (67%)
finalRisk = max(0.3, 0.6*0.4 + 0.67*0.6)
          = max(0.3, 0.24 + 0.402)
          = 0.642 → 64% MEDIUM risk
```

**2. AI Decision Phase**

The risk scores are sent to GitHub Copilot AI which:
- **High Risk (≥70%)**: Recommends scanning - likely has violations
- **Medium Risk (40-70%)**: Scans if file contains interactive components (forms, buttons, modals)
- **Low Risk (<40%)**: Can skip - proven clean or low violation history

If ALL files are unknown (80% risk), skips AI and scans everything to establish baseline.

**3. Orchestrated Scanning**

- Executes multiple scanning strategies in parallel
- Currently: Static ESLint scanner (extensible to add live scanning, custom checkers)
- Deduplicates violations found by multiple strategies

**4. Confidence Scoring**

Each violation receives a confidence score:
- **Static analysis**: 90% base confidence (highly reliable)
- **Pattern matching**: Boosted if similar violations seen before
- **WCAG Level**: Level A violations get higher confidence than AAA

**5. Priority Ranking**

Violations are ranked into tiers:
- **Critical**: Blocks core functionality (Level A failures)
- **High**: Significant barriers (serious severity)
- **Medium**: Important but not blocking
- **Low**: Minor improvements

### Static Scan Mode

Direct ESLint analysis with `eslint-plugin-jsx-a11y`:
- **Fast**: Milliseconds per file, no browser needed
- **Accurate**: Catches ~70% of common violations  
- **Limited**: Cannot detect runtime-only issues (dynamic ARIA, focus management, computed styles)
- **Best for**: Pre-commit hooks, CI/CD pipelines

### Live Scan Mode

Browser automation with axe-core:
- **Comprehensive**: Detects runtime violations static analysis misses
- **Slow**: Requires running application and browser launch
- **Real**: Tests actual rendered output users experience
- **Best for**: Integration testing, final QA before release

### Knowledge Base Storage

All results are automatically stored in `.a11y-guard/knowledge-base.db`:

```sql
-- Tracks which files are clean vs problematic
file_scans (file_path, violations_count, scan_type, timestamp)

-- Stores specific violations with context
violations (file_path, line_number, violation_rule, severity, 
            wcag_level, context, fixed, fix_commit_hash)

-- Learns common patterns and fix approaches
patterns (pattern_name, occurrences, fixes_applied, 
          fix_success_rate, common_fix, risk_score)
```

This historical data powers:
- **Risk scoring** for smart scan decisions
- **AI fix suggestions** with team-specific patterns  
- **Trend analysis** showing improvement over time
- **Baseline comparisons** to catch regressions

## Examples

### Compare Against Baseline (Regression Detection)

```bash
a11y-guard check --compare-baseline --block-on-regression
```

**Expected Output:**
```
🔍 Scanning with static mode...
📊 Loading baseline from last scan...

Current Scan: 12 violations
Baseline: 8 violations

Regression Analysis:
  ✓ 5 violations fixed since baseline
  ❌ 9 new violations introduced

New Violations:
  LoginForm.tsx (Line 34)
    Issue: Missing form label
    Introduced: This scan

  Navigation.tsx (Line 67, 89)
    Issue: Link missing href attribute
    Introduced: This scan

❌ BLOCKED: Regressions detected
   New violations: 9
   Fixed violations: 5
   Net change: +4 violations

Exit code: 1
```

### CI/CD Integration

```bash
# In CI pipeline
a11y-guard check --mode static --compare-baseline --block-on-regression
```

**Expected Output (if regressions found):**
```
❌ BLOCKED: Regressions detected
   New violations: 3
   Fixed violations: 1
   Net change: +2 violations

Exit code: 1
```

## How It Works

### 1. Static Mode (ESLint-based)

```typescript
// Static scanning process
async function staticScan(files: string[]) {
  const eslint = new ESLint({
    baseConfig: {
      extends: ['plugin:jsx-a11y/recommended'],
      plugins: ['jsx-a11y']
    }
  });
  
  const results = await eslint.lintFiles(files);
  
  // Filter for accessibility rules only
  const a11yViolations = results
    .flatMap(r => r.messages)
    .filter(m => m.ruleId?.startsWith('jsx-a11y/'));
  
  // Save to knowledge base
  await saveToKnowledgeBase(a11yViolations);
  
  return a11yViolations;
}
```

### 2. Live Mode (Browser-based)

```typescript
// Live scanning with Playwright + axe-core
async function liveScan(url: string) {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  
  await page.goto(url);
  
  // Inject axe-core
  await page.addScriptTag({
    url: 'https://cdn.jsdelivr.net/npm/axe-core'
  });
  
  // Run accessibility tests
  const results = await page.evaluate(() => {
    return axe.run();
  });
  
  // Process violations
  const violations = results.violations.map(v => ({
    rule: v.id,
    impact: v.impact,
    description: v.description,
    nodes: v.nodes.length,
    wcag: v.tags.filter(t => t.startsWith('wcag'))
  }));
  
  await browser.close();
  return violations;
}
```

### 3. Smart Mode (AI-powered)

```typescript
// Smart scanning with pattern matching
async function smartScan(files: string[]) {
  // Load learned patterns from KB
  const patterns = await knowledgeBase.getFixPatterns();
  const historicalViolations = await knowledgeBase.getViolations();
  
  // Analyze each file
  const predictions = [];
  for (const file of files) {
    const ast = parseToAST(file);
    
    // Match against known violation patterns
    for (const pattern of patterns) {
      const match = matchPattern(ast, pattern);
      if (match.confidence > 0.7) {
        predictions.push({
          file,
          prediction: pattern.violationType,
          confidence: match.confidence,
          context: match.context
        });
      }
    }
  }
  
  return predictions;
}
```

### 4. Baseline Comparison

```typescript
// Regression detection
async function compareBaseline(currentViolations) {
  const baseline = await knowledgeBase.getBaseline();
  
  const regression = {
    new: currentViolations.filter(v => 
      !baseline.find(b => 
        b.file === v.file && 
        b.line === v.line && 
        b.rule === v.rule
      )
    ),
    fixed: baseline.filter(b =>
      !currentViolations.find(v =>
        v.file === b.file &&
        v.line === b.line &&
        v.rule === b.rule
      )
    )
  };
  
  return regression;
}
```

## Common Use Cases

### 1. Pre-commit Hook Validation

**Scenario:** Automatically check staged files before commit.

```bash
# In .git/hooks/pre-commit
a11y-guard check --mode static --commit staged --block-on-regression

# Developer workflow
git add src/components/Button.tsx
git commit -m "Add new button component"

# Output if violations found:
# ❌ 2 violations in staged files
# Commit blocked. Fix violations or use --no-verify to skip.
```

### 2. CI/CD Pipeline Integration

**Scenario:** Fail CI build on accessibility regressions.

```yaml
# .github/workflows/a11y-check.yml
- name: Run accessibility scan
  run: |
    npm install -g a11y-guard
    a11y-guard init
    a11y-guard check \
      --files src/components/Button.tsx src/components/Form.tsx \
      --compare-baseline \
      --block-on-regression
```

### 3. Pull Request Review

**Scenario:** Check only changed files in PR.

```bash
# Get changed files in PR
CHANGED_FILES=$(git diff --name-only origin/main...HEAD | grep -E '\.(ts|tsx)$')

# Scan only changed files
a11y-guard check --files $CHANGED_FILES --compare-baseline
```

### 4. Comprehensive QA Scan

**Scenario:** Full project scan before release.

```bash
# Run all three modes for comprehensive coverage
echo "Running static analysis..."
a11y-guard check --mode static --files src/components/Button.tsx src/components/Form.tsx

echo "Starting dev server for live scan..."
npm run dev &
sleep 5

echo "Running live browser scan..."
a11y-guard check --mode live --url http://localhost:3000

echo "Running smart AI scan..."
a11y-guard check --mode smart

# View analysis
a11y-guard analyze
```

## Troubleshooting

### Issue: "No files found to scan"

**Cause:** Glob pattern doesn't match any files or wrong working directory.

**Solution:**
```bash
# Test glob pattern
ls src/**/*.tsx

# Use explicit paths
a11y-guard check src/components/Button.tsx src/pages/Home.tsx

# Check current directory
pwd
cd /path/to/project
a11y-guard check
```

### Issue: Live mode fails with "Cannot connect to URL"

**Cause:** Development server not running or wrong URL.

**Solution:**
```bash
# Start dev server first
npm run dev &

# Wait for server to start
sleep 5

# Check server is running
curl http://localhost:3000

# Then run scan
a11y-guard check --mode live --url http://localhost:3000
```

### Issue: "Baseline not found" when comparing

**Cause:** No previous scan to compare against.

**Solution:**
```bash
# Run initial scan to establish baseline
a11y-guard check --files "src/**/*.tsx"

# Now comparison will work
a11y-guard check --compare-baseline
```

### Issue: False positives in static mode

**Cause:** ESLint rules too strict or not understanding dynamic patterns.

**Solution:**
```bash
# Use smart mode instead
a11y-guard check --mode smart

# Or disable specific rules
# In .eslintrc.a11y.json:
{
  "rules": {
    "jsx-a11y/no-autofocus": "off"
  }
}
```

### Issue: Smart mode shows low confidence predictions

**Cause:** Insufficient learning data in knowledge base.

**Solution:**
```bash
# Build knowledge base from git history
a11y-guard learn --full

# Check knowledge base size
a11y-guard analyze

# Should have 50+ violations and fixes for good predictions
```

## Related Commands

- [fix](./fix.md) - Get AI-powered fix suggestions for detected violations
- [analyze](./analyze.md) - View patterns from historical scan results
- [learn](./learn.md) - Build knowledge base to improve smart mode accuracy
- [init](./init.md) - Set up git hooks that automatically run check

## Additional Notes

### Performance Considerations

- **Static mode**: Fast (~1-2 seconds for 50 files)
- **Live mode**: Slower (~5-10 seconds per URL)
- **Smart mode**: Medium (~3-5 seconds for 50 files)

### Exit Codes

- `0`: No violations or non-blocking violations
- `1`: Violations found with `--block-on-regression`
- `2`: Command error (invalid arguments, etc.)

### Supported File Types

Static mode supports:
- `.js`, `.jsx`
- `.ts`, `.tsx`
- `.vue` (experimental)
- `.svelte` (experimental)

### WCAG Coverage

The check command covers these WCAG 2.1 criteria:
- Level A: All criteria
- Level AA: All criteria
- Level AAA: Partial coverage

See [WCAG compliance matrix](../wcag-compliance.md) for details.
