# init

Initialize A11yGuard in your project with automated setup of database, git hooks, and configuration files.

## Syntax

```bash
a11y-guard init [options]
```

## Description

The `init` command sets up A11yGuard in your project by creating necessary directories, initializing the SQLite database, installing git hooks, and configuring ESLint integration. This is the first command you should run when adding A11yGuard to a new project.

The command performs the following actions:
- Creates `.a11y-guard/` directory in your project root
- Initializes SQLite database with schema for knowledge base
- Installs 3 git hooks: `pre-commit`, `pre-push`, and `post-merge`
- Creates `.a11yguard.eslint.json` rule override file
- Creates `.a11yguard.config.json` project configuration file
- Updates `.gitignore` to exclude `.a11y-guard/` directory
- Intelligently manages existing git hooks (backs up and appends rather than overwriting)

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--demo` | boolean | false | Populate the database with sample accessibility violations and fixes for demonstration purposes |

## Examples

### Basic Initialization

```bash
a11y-guard init
```

**Actual Output:**
```
⠙ Knowledge base initialized
ℹ️  To load demo data, run: npm run seed-data
✔ A11yGuard initialized successfully!

✅ A11yGuard is now protecting your accessibility!

Configuration:
  • Database: ./.a11y-guard/knowledge-base.db (project-specific)
  • ESLint rules: .a11yguard.eslint.json (customize rules here)
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

Next steps:
1. Run 'a11y-guard check' to scan for accessibility issues
2. Run 'a11y-guard learn' to build knowledge from git history
3. Run 'a11y-guard fix <file>' to get AI-powered fix suggestions
```

### Initialize with Demo Data

```bash
a11y-guard init --demo
```

**Expected Output:**
```
🚀 Initializing A11yGuard...

✓ Created .a11y-guard directory
✓ Initialized SQLite database
✓ Created database schema
✓ Seeded database with 50 sample violations
✓ Seeded database with 25 sample fixes
✓ Installed pre-commit hook
✓ Installed pre-push hook
✓ Installed post-merge hook
✓ Created .eslintrc.a11y.json
✓ Updated .gitignore

✨ A11yGuard initialized successfully with demo data!

Try these commands:
- 'a11y-guard analyze' to see violation patterns
- 'a11y-guard ask "What are common image alt text issues?"'
- 'a11y-guard check src/components/Button.tsx'
```

### Re-initialization (Existing Installation)

```bash
a11y-guard init
```

**Expected Output:**
```
⚠️  A11yGuard is already initialized in this project

Found existing:
- .a11y-guard/ directory
- SQLite database (150 violations, 75 fixes recorded)
- Git hooks (all 3 installed)

Would you like to:
1. Keep existing setup (recommended)
2. Reinstall hooks only
3. Reset database (⚠️  loses all data)
4. Full reset (⚠️  removes everything)

Choose [1-4]: 
```

## How It Works

### 1. Directory Structure Creation

```
project-root/
├── .a11y-guard/
│   ├── knowledge.db          # SQLite database
│   └── backups/               # Automatic backups
├── .git/
│   └── hooks/
│       ├── pre-commit         # Scans staged files
│       ├── pre-push           # Validates before push
│       └── post-merge         # Auto-learns from new commits
├── .a11yguard.eslint.json     # ESLint rule overrides (customizable)
├── .a11yguard.config.json     # A11yGuard project configuration
└── .gitignore                 # Updated to exclude .a11y-guard/
```

### 2. Database Schema Initialization

The SQLite database is created with these tables:
- `violations`: Records all detected accessibility issues
- `fixes`: Stores applied fixes and their outcomes
- `scan_sessions`: Tracks each scan run
- `kb_metadata`: Configuration and learning progress
- `fix_patterns`: Learned patterns from successful fixes

### 3. Smart Hook Management

When installing git hooks, the init command:

```typescript
// Pseudocode of hook installation logic
if (hookExists) {
  // Backup existing hook
  fs.copyFileSync(hookPath, `${hookPath}.backup-${timestamp}`);
  
  // Read existing content
  const existing = fs.readFileSync(hookPath, 'utf-8');
  
  // Check if A11yGuard hook already present
  if (existing.includes('a11y-guard')) {
    console.log('✓ Hook already configured');
  } else {
    // Append A11yGuard hook to existing
    fs.appendFileSync(hookPath, a11yGuardHookContent);
    console.log('✓ Updated existing hook');
  }
} else {
  // Create new hook
  fs.writeFileSync(hookPath, a11yGuardHookContent);
  fs.chmodSync(hookPath, '755'); // Make executable
  console.log('✓ Installed hook');
}
```

### 4. Git Hook Content

**pre-commit hook:**
```bash
#!/bin/sh
# A11yGuard pre-commit hook
a11y-guard check --mode static --commit staged --block-on-regression
```

**pre-push hook:**
```bash
#!/bin/sh
# A11yGuard pre-push hook
a11y-guard check --mode smart --compare-baseline --block-on-regression
```

**post-merge hook:**
```bash
#!/bin/sh
# A11yGuard post-merge hook
a11y-guard learn --incremental --silent
```

## Common Use Cases

### 1. New Project Setup

**Scenario:** Setting up A11yGuard in a fresh React project.

```bash
# Navigate to project
cd my-react-app

# Initialize A11yGuard
a11y-guard init

# Build knowledge from existing git history
a11y-guard learn --full

# Run initial scan
a11y-guard check src/
```

### 2. Team Onboarding

**Scenario:** Getting a new developer set up with A11yGuard.

```bash
# Clone repository
git clone https://github.com/company/app.git
cd app

# Install dependencies
npm install

# Initialize A11yGuard (hooks auto-installed)
a11y-guard init

# Test setup
a11y-guard ask "What accessibility rules does our team enforce?"
```

### 3. CI/CD Pipeline Setup

**Scenario:** Adding A11yGuard to CI without git hooks.

```bash
# In CI script (no hooks needed)
npm install -g a11y-guard

# Initialize database only (skip hooks in CI)
a11y-guard init --skip-hooks

# Run checks
a11y-guard check --mode static --files "src/**/*.{ts,tsx}"
```

### 4. Demo Environment

**Scenario:** Trying out A11yGuard features without scanning real code.

```bash
# Initialize with sample data
a11y-guard init --demo

# Explore features with demo data
a11y-guard analyze
a11y-guard ask "Show me common button accessibility issues"
```

## Troubleshooting

### Issue: "Permission denied" when installing hooks

**Cause:** Insufficient permissions to write to `.git/hooks/` directory.

**Solution:**
```bash
# Check directory permissions
ls -la .git/hooks/

# Fix permissions
chmod 755 .git/hooks/

# Retry initialization
a11y-guard init
```

### Issue: "Not a git repository"

**Cause:** Running `init` outside a git repository.

**Solution:**
```bash
# Initialize git first
git init

# Then initialize A11yGuard
a11y-guard init
```

### Issue: Existing hooks are overwritten

**Cause:** This shouldn't happen, but if it does...

**Solution:**
```bash
# Restore from backup
cp .git/hooks/pre-commit.backup-* .git/hooks/pre-commit

# Re-run init (will append properly)
a11y-guard init
```

### Issue: ".a11y-guard is not ignored by git"

**Cause:** `.gitignore` update failed or was manually removed.

**Solution:**
```bash
# Manually add to .gitignore
echo ".a11y-guard/" >> .gitignore

# Remove from git if already tracked
git rm -r --cached .a11y-guard/
git commit -m "Ignore .a11y-guard directory"
```

### Issue: "Database initialization failed"

**Cause:** Corrupted previous installation or disk space issues.

**Solution:**
```bash
# Remove existing installation
rm -rf .a11y-guard/

# Check disk space
df -h

# Retry initialization
a11y-guard init
```

### Issue: Hooks not executing on commit

**Cause:** Hooks not executable or git configuration.

**Solution:**
```bash
# Make hooks executable
chmod +x .git/hooks/pre-commit
chmod +x .git/hooks/pre-push
chmod +x .git/hooks/post-merge

# Test hook directly
.git/hooks/pre-commit

# Check git config
git config core.hooksPath  # Should be empty or .git/hooks
```

## Customizing ESLint Rules

The `init` command creates `.a11yguard.eslint.json` which allows you to customize accessibility rules without installing any dependencies. A11yGuard uses ESLint with `jsx-a11y` plugin internally and merges your rule overrides with its defaults.

### Understanding the Override File

The `.a11yguard.eslint.json` file is created with documentation and examples:

```json
{
  "$schema": "https://json.schemastore.org/eslintrc",
  "_comment": "A11yGuard ESLint Rule Overrides - Customize accessibility rules for your project",
  "_usage": [
    "This file allows you to customize ESLint jsx-a11y rules without installing dependencies.",
    "Rules defined here will override A11yGuard's defaults.",
    "Set rules to 'off', 'warn', or 'error' to customize behavior.",
    "Example: To disable autofocus warnings, set 'jsx-a11y/no-autofocus': 'off'",
    "See all available rules: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y#supported-rules"
  ],
  "rules": {
    // Your custom rule overrides go here
  }
}
```

### How It Works

1. **Default Rules**: A11yGuard has 28 built-in jsx-a11y rules enabled by default, all set to `"error"`
2. **Override Mechanism**: Rules in `.a11yguard.eslint.json` override the defaults
3. **No Dependencies**: You don't need to install `eslint-plugin-jsx-a11y` in your project
4. **Dynamic Loading**: The scanner automatically detects and applies your overrides

### Common Customizations

**Disable a specific rule:**
```json
{
  "rules": {
    "jsx-a11y/no-autofocus": "off"
  }
}
```

**Change severity from error to warning:**
```json
{
  "rules": {
    "jsx-a11y/click-events-have-key-events": "warn",
    "jsx-a11y/no-static-element-interactions": "warn"
  }
}
```

**Configure rule options:**
```json
{
  "rules": {
    "jsx-a11y/label-has-associated-control": ["error", {
      "assert": "either",
      "depth": 3
    }],
    "jsx-a11y/alt-text": ["error", {
      "elements": ["img", "object", "area", "input[type='image']"],
      "img": ["Image", "Photo"]
    }]
  }
}
```

**Disable multiple rules for gradual adoption:**
```json
{
  "rules": {
    "jsx-a11y/click-events-have-key-events": "warn",
    "jsx-a11y/no-static-element-interactions": "warn",
    "jsx-a11y/no-noninteractive-element-interactions": "warn",
    "jsx-a11y/mouse-events-have-key-events": "warn"
  }
}
```

### Default Rules Reference

A11yGuard enables these jsx-a11y rules by default (all set to `"error"`):

| Rule | Description |
|------|-------------|
| `alt-text` | Require alt text on images |
| `anchor-has-content` | Anchors must have content |
| `anchor-is-valid` | Anchors must have valid href |
| `aria-activedescendant-has-tabindex` | Elements with aria-activedescendant must be tabbable |
| `aria-props` | ARIA properties must be valid |
| `aria-proptypes` | ARIA property values must be valid |
| `aria-role` | ARIA roles must be valid |
| `aria-unsupported-elements` | ARIA is not allowed on certain elements |
| `click-events-have-key-events` | Click handlers must have keyboard support |
| `heading-has-content` | Headings must have content |
| `html-has-lang` | HTML element must have lang attribute |
| `iframe-has-title` | iframes must have title attribute |
| `img-redundant-alt` | Alt text should not contain "image" or "picture" |
| `interactive-supports-focus` | Interactive elements must be focusable |
| `label-has-associated-control` | Labels must be associated with a control |
| `media-has-caption` | Media elements must have captions |
| `mouse-events-have-key-events` | Mouse events must have keyboard equivalents |
| `no-access-key` | Avoid using accessKey attribute |
| `no-autofocus` | Avoid using autoFocus attribute |
| `no-distracting-elements` | Avoid distracting elements like <marquee> |
| `no-noninteractive-element-interactions` | Non-interactive elements should not have handlers |
| `no-noninteractive-tabindex` | Non-interactive elements should not be tabbable |
| `no-redundant-roles` | Avoid redundant ARIA roles |
| `no-static-element-interactions` | Static elements should not have handlers |
| `role-has-required-aria-props` | ARIA roles must have required properties |
| `role-supports-aria-props` | ARIA properties must be supported by role |
| `scope` | Scope attribute must only be on <th> elements |
| `tabindex-no-positive` | Avoid positive tabindex values |

### Verification

When you run a scan, A11yGuard will display a message if it loads custom overrides:

```bash
a11y-guard check --mode=static
```

**Output:**
```
📋 Static Scan - ESLint with jsx-a11y
[Static Scanner] Loaded 1 rule override(s) from .a11yguard.eslint.json
...
```

### Team Collaboration

**Commit the override file:**
```bash
git add .a11yguard.eslint.json
git commit -m "Configure project-specific accessibility rules"
```

This ensures all team members use the same accessibility rule configuration.

## Related Commands

- [learn](./learn.md) - Build knowledge base from git history (recommended after init)
- [check](./check.md) - Scan for accessibility violations
- [fix](./fix.md) - Get AI-powered fix suggestions
- [analyze](./analyze.md) - View patterns in your knowledge base

## Additional Notes

### Database Location

The SQLite database is stored at `.a11y-guard/knowledge.db`. This file contains:
- All detected violations
- Applied fixes and their success rates
- Learned patterns from git history
- Team-specific accessibility knowledge

### Hook Behavior

Git hooks can be temporarily bypassed using:
```bash
# Skip hooks for a single commit
git commit --no-verify

# Skip hooks for a single push
git push --no-verify
```

However, this is not recommended as it bypasses accessibility checks.

### Uninstallation

To remove A11yGuard from your project:
```bash
# Remove directory and database
rm -rf .a11y-guard/

# Remove hooks
rm .git/hooks/pre-commit
rm .git/hooks/pre-push
rm .git/hooks/post-merge

# Remove config files
rm .a11yguard.eslint.json
rm .a11yguard.config.json

# Remove from .gitignore
# (manually edit .gitignore to remove .a11y-guard/ line)
```

### Network Requirements

The `init` command works completely offline. It does not require:
- Internet connection
- External API calls
- Package downloads (beyond the CLI itself)

All setup is local to your project.
