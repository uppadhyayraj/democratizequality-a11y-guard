# 🚀 Getting Started with A11yGuard

**Complete onboarding guide with sample React application**

This guide will walk you through setting up A11yGuard in a real React project, using our demo application that contains intentional accessibility violations for testing.

---

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setting Up the Demo App](#setting-up-the-demo-app)
3. [Initializing A11yGuard](#initializing-a11yguard)
4. [Populating Sample Data](#populating-sample-data)
5. [Running Your First Scans](#running-your-first-scans)
6. [Fixing Issues Interactively](#fixing-issues-interactively)
7. [Team Collaboration Workflow](#team-collaboration-workflow)
8. [Next Steps](#next-steps)

---

## Prerequisites

### 1. Install GitHub Copilot CLI

A11yGuard requires the GitHub Copilot CLI to power its AI capabilities.

```bash
# Check if already installed
copilot --version

# If not installed, follow the official guide:
# https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli
```

**Installation Steps:**

**macOS/Linux:**
```bash
# Using Homebrew (macOS)
brew install gh copilot

# Or download from GitHub releases
# https://github.com/cli/cli/releases
```

**Windows:**
```bash
# Using winget
winget install GitHub.cli

# Or download installer from:
# https://github.com/cli/cli/releases
```

**Authenticate:**
```bash
# Authenticate with GitHub
gh auth login

# Verify Copilot is working
copilot --version
copilot --status
```

### 2. Verify Node.js

```bash
node --version  # Should be 18.0.0 or higher
npm --version   # Should be 9.0.0 or higher
```

If you need to install/upgrade Node.js:
- **macOS/Linux**: Use [nvm](https://github.com/nvm-sh/nvm)
- **Windows**: Download from [nodejs.org](https://nodejs.org/)

### 3. GitHub Copilot Subscription

You need an active GitHub Copilot subscription:
- **Copilot Individual** ($10/month)
- **Copilot Business** (via your organization)
- **Copilot Enterprise** (via your organization)

[Sign up for Copilot →](https://github.com/features/copilot)

---

## Setting Up the Demo App

The demo app is a React application with **intentional accessibility violations** for testing A11yGuard.

### Step 1: Clone or Navigate to Demo App

```bash
# If using A11yGuard repository
cd a11y-guard/demo-app

# Or create your own React app
npx create-vite@latest my-accessible-app --template react-ts
cd my-accessible-app
```

### Step 2: Install Dependencies

```bash
# Install project dependencies
npm install

# Install A11yGuard globally
npm install -g a11y-guard

# Verify installation
a11y-guard --version
```

### Step 3: Explore the Demo App

The demo app contains these components with accessibility issues:

```
demo-app/src/components/
  ├── LoginForm.tsx        ← Missing labels, no keyboard support
  ├── ImageGallery.tsx     ← Missing alt text
  ├── Navigation.tsx       ← Invalid heading hierarchy
  ├── Modal.tsx            ← Focus trap issues
  ├── ClickableDiv.tsx     ← Non-semantic interactive elements
  ├── IconButton.tsx       ← Missing accessible names
  ├── HeadingStructure.tsx ← Skipped heading levels
  └── SearchBox.tsx        ← Autocomplete issues
```

### Step 4: Run the Demo App

```bash
# Start development server
npm run dev

# Open in browser
# http://localhost:5173
```

You'll see a functional app, but try using it with:
- **Keyboard only** (Tab, Enter, Space)
- **Screen reader** (VoiceOver on Mac, NVDA/JAWS on Windows)

You'll quickly discover the accessibility issues!

---

## Initializing A11yGuard

Now let's set up A11yGuard in the project.

### Step 1: Initialize A11yGuard

```bash
# From project root (demo-app/)
a11y-guard init
```

**Expected output:**
```
✔ A11yGuard initialized successfully!

✅ A11yGuard is now protecting your accessibility!

Configuration:
  • Database: ./.a11y-guard/knowledge-base.db (project-specific)
  • ESLint config: .eslintrc.a11y.json (A11yGuard's config)
  • Git hooks: pre-commit, pre-push, post-merge
  • Config: .a11yguard.config.json

Next steps:
  • Run: a11y-guard check
  • Run: a11y-guard check --compare-baseline
  • Ask: a11y-guard ask "What should I watch for in button components?"

Git Integration:
  • pre-commit: Blocks commits with accessibility regressions
  • pre-push: Warns about accessibility issues before push
  • post-merge: Auto-syncs KB after git pull
  • Customize behavior in .a11yguard.config.json
```

### Step 2: Verify File Structure

```bash
ls -la

# You should see:
# .a11y-guard/              ← Knowledge base directory
# .a11yguard.config.json    ← Configuration file
# .eslintrc.a11y.json       ← ESLint accessibility rules
# .gitignore                ← Updated with .a11y-guard/
```

### Step 3: Verify Git Hooks

```bash
ls -la .git/hooks/

# You should see:
# -rwxr-xr-x  pre-commit   ← Blocks regressions
# -rwxr-xr-x  pre-push     ← Warns before push
# -rwxr-xr-x  post-merge   ← Auto-syncs KB
```

---

## Populating Sample Data

A11yGuard learns from your git history. Let's populate the knowledge base with sample data.

### Option 1: Learn from Existing Git History

If you have existing commits:

```bash
# Learn from last 100 commits (default)
a11y-guard learn

# Learn from last 200 commits
a11y-guard learn --commits 200

# Full re-learn
a11y-guard learn --full
```

**Expected output:**
```
✔ Learned from 47 commits

✅ Knowledge base updated!

Learned from:
  • 47 commits analyzed
  • 12 commits with accessibility fixes
  • 18 fix patterns recorded

💡 Tip: Run "a11y-guard analyze" to see patterns
   Future "git pull" operations will auto-sync new commits
```

### Option 2: Use Sample Data Script

If you're in the demo-app directory:

```bash
# Run the sample data script
cd ../demo/seed-data
npm install
npx tsx import-seed.ts

# This will:
# - Create sample violations
# - Record successful fixes
# - Populate the knowledge base
```

### Option 3: Start Fresh (Create Your Own Patterns)

If you want to learn from scratch as you fix issues:

```bash
# Just run your first check
a11y-guard check

# The knowledge base will learn as you:
# 1. Find violations
# 2. Fix them
# 3. Commit your fixes
# 4. A11yGuard learns from the diffs
```

---

## Running Your First Scans

### Scan 1: Quick Static Check

```bash
# Fast ESLint-based scan
a11y-guard check --mode=static
```

**Expected output:**
```
✔ Scan complete!

📊 Accessibility Violations Found:

❌ src/components/LoginForm.tsx (3 violations)
  • Line 15: Form elements must have labels (jsx-a11y/label-has-associated-control)
  • Line 23: Visible, non-interactive elements with click handlers must have role (jsx-a11y/click-events-have-key-events)
  • Line 31: Elements with onClick must have keyboard equivalent (jsx-a11y/interactive-supports-focus)

❌ src/components/ImageGallery.tsx (2 violations)
  • Line 8: img elements must have alt prop (jsx-a11y/alt-text)
  • Line 12: img elements must have alt prop (jsx-a11y/alt-text)

Summary:
  • 5 violations found
  • 2 files affected
  • 3 violation types
```

### Scan 2: Smart Mode (AI-Predicted)

```bash
# AI predicts which files are likely to have issues
a11y-guard check --mode=smart
```

The AI will prioritize:
- Recently changed files
- Files with historical violations
- Files matching patterns known to have issues

### Scan 3: Live Browser Testing

```bash
# Start your dev server first
npm run dev

# Then run live scan
a11y-guard check --mode=live --url=http://localhost:5173
```

This performs **real browser testing** with:
- Accessibility tree inspection
- Color contrast checking
- ARIA attribute validation
- Keyboard navigation testing

### Scan 4: Compare with Baseline

```bash
# Compare current state with baseline
a11y-guard check --compare-baseline

# This shows:
# ✅ Fixed issues (green)
# ❌ New regressions (red)
# ⚠️ Existing issues (yellow)
```

---

## Fixing Issues Interactively

Now comes the powerful part - AI-powered interactive fixes!

### Example 1: Fix LoginForm.tsx

```bash
# Start interactive fix session
a11y-guard fix src/components/LoginForm.tsx
```

**Expected interaction:**
```
🔍 Scanning src/components/LoginForm.tsx...

Found 3 accessibility violations:

──────────────────────────────────────────
Issue 1 of 3: Form elements must have labels
──────────────────────────────────────────

📍 Location: src/components/LoginForm.tsx:15
🏷️  Rule: jsx-a11y/label-has-associated-control

Current code:
  13 | <div className="form-group">
  14 |   <input
  15 |     type="email"
  16 |     name="email"
  17 |     placeholder="Email"
  18 |   />
  19 | </div>

💡 AI Recommendation:

Associate the input with a label using htmlFor and id attributes:

<div className="form-group">
  <label htmlFor="email-input">Email</label>
  <input
    id="email-input"
    type="email"
    name="email"
    placeholder="Email"
  />
</div>

📚 WCAG 2.1 AA: Form inputs must have programmatically associated labels
   for screen reader users.

✅ Similar fixes worked 5 times in your team's history

Apply this fix? (y/n/s/q)
  y = yes, apply
  n = no, skip
  s = show full diff
  q = quit

Your choice:
```

**Your response:**
```
> y
```

**Result:**
```
✅ Fix applied successfully!

📊 Token Usage:
  Input tokens:    15,234 (12,891 cached)
  Output tokens:    456
  Cache tokens:    12,891
  Total tokens:    15,690

──────────────────────────────────────────
Issue 2 of 3: Missing keyboard support
──────────────────────────────────────────
...
```

### Example 2: Non-Interactive Mode

```bash
# Just show suggestions without applying
a11y-guard fix src/components/ImageGallery.tsx --no-interactive
```

**Output:**
```
🔍 Scanning src/components/ImageGallery.tsx...

Found 2 accessibility violations:

══════════════════════════════════════════
Suggestion 1 of 2
══════════════════════════════════════════

📍 src/components/ImageGallery.tsx:8
🏷️  jsx-a11y/alt-text

Add descriptive alt text:

- <img src={image.url} />
+ <img src={image.url} alt={image.description || 'Gallery image'} />

══════════════════════════════════════════
Suggestion 2 of 2
══════════════════════════════════════════
...

🤖 Model: gpt-4.1
📊 Tokens: 8,445 total (5,123 input, 3,322 output)
```

---

## Team Collaboration Workflow

A11yGuard shines with team collaboration. Here's how it works:

### Scenario: Developer A Fixes an Issue

**Developer A:**
```bash
# 1. Find and fix accessibility issue
a11y-guard fix src/components/Button.tsx

# 2. Apply fix interactively
# (adds aria-label to button)

# 3. Commit the fix
git add src/components/Button.tsx
git commit -m "fix: add aria-label to icon button"

# Pre-commit hook runs:
# ✅ A11yGuard: No regressions detected

# 4. Push to remote
git push origin main

# Pre-push hook runs:
# ✅ Pre-push check complete
```

### Scenario: Developer B Gets the Fix

**Developer B (on a different machine):**
```bash
# 1. Pull latest changes
git pull origin main

# Post-merge hook runs automatically:
# 🛡️  A11yGuard: Syncing knowledge base...
# ✓ Learned 3 fix patterns from 1 commits

# 2. Now when Developer B runs fix command
a11y-guard fix src/components/IconButton.tsx

# The AI suggests the SAME fix pattern Developer A used!
# "Similar fixes worked 6 times in your team's history"
```

### Scenario: Check Before Committing

```bash
# Developer C makes changes
vim src/components/Modal.tsx

# Check before committing
a11y-guard check --compare-baseline

# Output shows:
# ✅ 2 issues fixed
# ❌ 1 new regression!

# Fix the regression
a11y-guard fix src/components/Modal.tsx

# Now commit
git add .
git commit -m "feat: improve modal accessibility"

# Pre-commit hook: ✅ No regressions
```

### Incremental Learning

```bash
# Knowledge base updates automatically:

# On git pull  → post-merge hook → learn --incremental --silent
# On git merge → post-merge hook → learn --incremental --silent

# Manual incremental learn (only new commits):
a11y-guard learn --incremental

# Full re-learn (all commits):
a11y-guard learn --full --commits 200
```

---

## Next Steps

### 1. Explore Advanced Features

```bash
# Ask questions about accessibility
a11y-guard ask "What are ARIA live regions?"

# Analyze patterns
a11y-guard analyze

# Check specific commit
a11y-guard check --commit abc123

# Check specific files
a11y-guard check --files src/components/Nav.tsx src/components/Header.tsx
```

### 2. Customize Configuration

Edit `.a11yguard.config.json`:

```json
{
  "version": "1.0.0",
  "enforcement": {
    "level": "warn",
    "blockOnRegression": true,
    "blockOnNewViolations": false
  },
  "scanning": {
    "defaultMode": "smart",
    "includePatterns": [
      "src/**/*.{ts,tsx,js,jsx}"
    ],
    "excludePatterns": [
      "node_modules/**",
      "dist/**",
      "build/**",
      "**/*.test.{ts,tsx,js,jsx}",
      "**/*.spec.{ts,tsx,js,jsx}"
    ]
  },
  "wcag": {
    "level": "AA",
    "rules": {}
  },
  "ai": {
    "model": "claude-sonnet-4.5",
    "timeout": 60000
  },
  "gitHooks": {
    "preCommit": true,
    "prePush": false,
    "compareBaseline": true
  },
  "reporting": {
    "verbose": false,
    "showFixSuggestions": true,
    "showHistoricalContext": true
  }
}
```

**Configuration Options:**

- **`enforcement.level`**: `'strict'` | `'warn'` | `'off'` - How aggressively to enforce rules
- **`enforcement.blockOnRegression`**: Block commits if accessibility gets worse
- **`enforcement.blockOnNewViolations`**: Block commits with any new violations
- **`scanning.defaultMode`**: `'static'` | `'smart'` | `'live'` - Default scan strategy
- **`scanning.includePatterns`**: Glob patterns for files to scan
- **`scanning.excludePatterns`**: Glob patterns for files to ignore
- **`wcag.level`**: `'A'` | `'AA'` | `'AAA'` - WCAG compliance level to target
- **`ai.model`**: AI model to use - options:
  - `'claude-sonnet-4.5'` (default, best balance)
  - `'gpt-4o'` (OpenAI GPT-4 Optimized)
  - `'gpt-4.1'` (OpenAI GPT-4.1)
  - `'o1'` (OpenAI o1 reasoning model)
- **`ai.timeout`**: Request timeout in milliseconds (default: 60000)
- **`gitHooks.preCommit`**: Enable pre-commit hook (blocks regressions)
- **`gitHooks.prePush`**: Enable pre-push hook (warns about issues)
- **`gitHooks.compareBaseline`**: Compare against baseline in hooks
- **`reporting.verbose`**: Show detailed output
- **`reporting.showFixSuggestions`**: Display AI-powered fix suggestions
- **`reporting.showHistoricalContext`**: Show similar past violations

### 3. Integrate with CI/CD

**.github/workflows/a11y-check.yml:**
```yaml
name: Accessibility Check

on: [pull_request]

jobs:
  a11y:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install A11yGuard
        run: npm install -g a11y-guard
      
      - name: Run accessibility check
        run: a11y-guard check --mode=static --compare-baseline
```

### 4. Learn More

- [📘 Command Reference](../README.md#command-reference)
- [📘 init command](commands/init.md)
- [📘 check command](commands/check.md)
- [📘 fix command](commands/fix.md)
- [📘 analyze command](commands/analyze.md)
- [📘 ask command](commands/ask.md)
- [📘 learn command](commands/learn.md)

---

## Troubleshooting

### Issue: "Copilot CLI not found"

```bash
# Verify installation
which copilot
copilot --version

# If not found, reinstall:
brew install gh copilot  # macOS
winget install GitHub.cli  # Windows

# Authenticate
gh auth login
copilot --status
```

### Issue: "Git hooks not running"

```bash
# Check hooks are executable
ls -la .git/hooks/pre-commit

# Should show: -rwxr-xr-x

# Re-initialize if needed
a11y-guard init
```

### Issue: "No violations found but accessibility issues exist"

```bash
# Try different scan modes

# 1. Static (fast, may miss runtime issues)
a11y-guard check --mode=static

# 2. Live (thorough, requires running app)
npm run dev  # Start app first
a11y-guard check --mode=live --url=http://localhost:5173

# 3. Smart (AI-predicted, balanced)
a11y-guard check --mode=smart
```

### Issue: "Knowledge base not learning from commits"

```bash
# Check last learned commit
sqlite3 .a11y-guard/knowledge-base.db "SELECT value FROM kb_metadata WHERE key='last_learned_commit'"

# Force full re-learn
a11y-guard learn --full --commits 100

# Check post-merge hook
cat .git/hooks/post-merge
```

### Issue: "Token usage too high"

```bash
# Use non-interactive mode for viewing suggestions
a11y-guard fix --no-interactive <files>

# Limit to specific files
a11y-guard check --files src/components/specific.tsx

# Use static mode (no AI)
a11y-guard check --mode=static
```

---

## Tips & Best Practices

### 🎯 Development Workflow Tips

1. **Fix issues early**: Run `a11y-guard fix` while developing, not after
2. **Use interactive mode**: Learn WCAG standards while fixing
3. **Review AI suggestions**: Don't blindly apply - understand the fix
4. **Check before committing**: Run `a11y-guard check --compare-baseline`

### 🤝 Team Collaboration Tips

1. **Pull regularly**: Let post-merge hook keep KB in sync
2. **Share patterns**: Commit accessibility fixes with clear messages
3. **Document decisions**: Use commit messages to explain why
4. **Learn together**: Use `a11y-guard ask` for team discussions

### ⚡ Performance Tips

1. **Use static mode** for quick checks during development
2. **Use smart mode** for balanced performance
3. **Use live mode** only for thorough pre-release testing
4. **Exclude test files** in `.a11yguard.config.json`

### 🔒 Security & Privacy Tips

1. **Knowledge base is local**: No data leaves your machine
2. **Git history analysis**: Diffs analyzed locally, not sent to cloud
3. **Copilot usage**: Only fix suggestions use Copilot API
4. **Team sync**: Via git history, not centralized database

---

## Success Checklist

After completing this guide, you should be able to:

- ✅ Initialize A11yGuard in a project
- ✅ Run different scan modes (static, live, smart)
- ✅ Fix issues interactively with AI assistance
- ✅ Compare against baseline to detect regressions
- ✅ Ask questions about accessibility
- ✅ Analyze patterns from your codebase
- ✅ Build team knowledge from git history
- ✅ Have git hooks working automatically
- ✅ Understand incremental learning
- ✅ Customize A11yGuard configuration

---

## What's Next?

🎓 **Learn Advanced Topics:**
- Multi-agent orchestration internals
- Custom fix pattern training
- MCP server integration
- VS Code extension usage

🚀 **Try Real-World Scenarios:**
- Fix all issues in the demo app
- Integrate A11yGuard into your production app
- Set up CI/CD with accessibility gates
- Share learnings with your team

💬 **Get Help:**
- Check command documentation
- Review troubleshooting section
- Ask A11yGuard: `a11y-guard ask "your question"`

---

**Congratulations!** You've successfully onboarded to A11yGuard. Now go make the web more accessible! 🛡️

*Happy accessible coding!* ♿✨
