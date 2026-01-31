# fix

Get AI-powered accessibility fix suggestions with interactive review and application.

## Syntax

```bash
a11y-guard fix <files...> [options]
```

## Description

The `fix` command uses AI to analyze accessibility violations and generate contextual fix suggestions. It employs a multi-agent architecture with 6 specialized AI agents that collaborate to provide accurate, safe, and educational fixes.

By default, the command runs in **interactive mode**, allowing you to review each suggestion, see explanations, and choose whether to apply the fix. It shows the exact code changes before applying them, making the process safe and transparent.

The command leverages your team's knowledge base, learning from previous fixes and patterns to provide increasingly accurate suggestions over time.

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `-i, --interactive` | boolean | `true` | Interactive mode: review and apply fixes one-by-one |
| `--no-interactive` | boolean | false | Non-interactive mode: only show suggestions without applying |

## Examples

### Non-Interactive Mode (View Only)

```bash
a11y-guard fix --no-interactive src/components/LoginForm.tsx
```

**Actual Output:**
```
- Scanning for accessibility issues...

📋 Static Scan - ESLint with jsx-a11y

Scanning 1 file:

  📄 LoginForm.tsx (src/components)

[Static Scanner] Found 1 violations in 483ms
✔ Found 1 violations

🤖 AI-Powered Fix Suggestions
Using: GitHub Copilot (Model: claude-sonnet-4.5)


🔧 Fix Recommendations:


📄 LoginForm.tsx
   /Users/rajuppadhyay/demo-a11y-test/src/components/LoginForm.tsx
   1 issue

   ❌ Line 42: jsx-a11y/label-has-associated-control
      {"message":"A form label must be associated with a control.","column":11,"endLine":42,"endColumn":18}

- Generating fix suggestion...
   💡 Suggested Fix:

   Perfect! I can see the issue. Here's how to fix it:
   
   ## 1. **Why this is a problem:**
   Form labels must be programmatically associated with their inputs so screen readers can announce 
   the label when users focus on the control. Without proper association, users with visual 
   impairments won't know what the checkbox is for.
   
   ## 2. **The specific code change:**
   You need to connect the label to the checkbox using either:
   - The `htmlFor` attribute on the label matching the `id` on the input, OR
   - Wrap the input inside the label element
   
   ## 3. **Fixed code example:**
   
   **Option A - Using htmlFor/id (Recommended):**
   ```tsx
   <div className="form-group">
     <label htmlFor="remember-me">Remember me</label>
     <input type="checkbox" id="remember-me" />
   </div>
   ```
   
   **Option B - Wrapping input in label:**
   ```tsx
   <div className="form-group">
     <label>
       <input type="checkbox" />
       Remember me
     </label>
   </div>
   ```
   
   Would you like me to apply Option A (the recommended approach) to your LoginForm.tsx file?

   ─────────────────────────────────────────────────


💡 Tips:
  • Use "a11y-guard ask <question>" to learn more about WCAG
  • Use "a11y-guard analyze" to see patterns across your codebase
  • Run "a11y-guard check" after applying fixes to verify


📊 Token Usage

💰 Token Usage
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Input tokens:  11415
Output tokens: 279
Cache tokens:  10352
Total tokens:  11694
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Basic Interactive Fix (Default Mode)

```bash
a11y-guard fix src/components/LoginForm.tsx
```

In interactive mode (the default), the command shows each violation with a detailed explanation and prompts you to apply the fix. You can review the exact code changes before they're applied. Interactive mode provides a safer, more educational experience where you learn about each accessibility issue as you fix it.

The output is similar to non-interactive mode but includes prompts for user input after each fix suggestion.

## How It Works

### Multi-Agent Architecture

The fix command orchestrates 6 specialized AI agents:

```typescript
// Agent orchestration
async function generateFixes(file: string) {
  // 1. Scanner Agent: Detect violations
  const violations = await scannerAgent.scan(file);
  
  // 2. History Agent: Find similar past violations
  const context = await historyAgent.findSimilar(violations);
  
  // 3. Fix Agent: Generate fix suggestions
  const fixes = await fixAgent.generate(violations, context);
  
  // 4. Educator Agent: Add explanations and WCAG refs
  const explained = await educatorAgent.explain(fixes);
  
  // 5. Risk Agent: Assess safety and side effects
  const assessed = await riskAgent.assess(explained);
  
  // 6. GitHub Agent: Validate against team patterns
  const validated = await githubAgent.validate(assessed);
  
  return validated;
}
```

### Agent Responsibilities

**1. Scanner Agent**
- Runs static and smart analysis
- Identifies specific violations
- Extracts code context

**2. History Agent**
- Queries knowledge base
- Finds similar violations
- Retrieves successful fix patterns
- Provides historical success rates

**3. Fix Agent**
- Generates code fixes
- Considers multiple approaches
- Preserves code style and patterns
- Ensures syntactic correctness

**4. Educator Agent**
- Explains why the fix is needed
- References WCAG criteria
- Provides learning resources
- Contextualizes for user's codebase

**5. Risk Agent**
- Analyzes potential side effects
- Assesses confidence scores
- Flags breaking changes
- Recommends testing approaches

**6. GitHub Agent**
- Checks team coding standards
- Validates against common patterns
- Ensures consistency with existing fixes
- Adapts to team preferences

### Interactive Review Process

```typescript
// Interactive fix application
async function interactiveFix(fixes: Fix[]) {
  for (const [index, fix] of fixes.entries()) {
    console.log(`\nFix ${index + 1} of ${fixes.length}\n`);
    displayFix(fix);
    
    const choice = await prompt('Apply this fix? [y/n/s/q]');
    
    switch (choice) {
      case 'y':
        await applyFix(fix);
        await saveToKnowledgeBase(fix, 'applied');
        console.log('✅ Applied');
        break;
      case 'n':
        await saveToKnowledgeBase(fix, 'rejected');
        console.log('⏭️  Skipped');
        break;
      case 's':
        console.log('⏭️  Skipped (not recorded)');
        break;
      case 'q':
        console.log('🛑 Quit (remaining fixes not shown)');
        return;
    }
  }
}
```

### Code Patching

```typescript
// Safe code modification
async function applyFix(fix: Fix) {
  const source = await fs.readFile(fix.file, 'utf-8');
  const lines = source.split('\n');
  
  // Validate line numbers
  if (fix.endLine > lines.length) {
    throw new Error('Invalid line range');
  }
  
  // Create backup
  await fs.copyFile(fix.file, `${fix.file}.backup`);
  
  // Apply fix
  const before = lines.slice(0, fix.startLine - 1);
  const after = lines.slice(fix.endLine);
  const modified = [...before, ...fix.newCode, ...after];
  
  // Write with atomic operation
  const tempFile = `${fix.file}.tmp`;
  await fs.writeFile(tempFile, modified.join('\n'));
  await fs.rename(tempFile, fix.file);
  
  // Verify syntax
  try {
    await validateSyntax(fix.file);
    await fs.unlink(`${fix.file}.backup`); // Remove backup
  } catch (error) {
    // Restore from backup on syntax error
    await fs.copyFile(`${fix.file}.backup`, fix.file);
    throw error;
  }
}
```

## Common Use Cases

### 1. Fixing Violations Before Commit

**Scenario:** You're about to commit but pre-commit hook found violations.

```bash
# Pre-commit hook blocks
git commit -m "Add new feature"
# ❌ 3 violations in Button.tsx

# Fix interactively
a11y-guard fix src/components/Button.tsx

# Review and apply fixes
# [Interactive session]

# Try commit again
git commit -m "Add new feature"
# ✅ All violations fixed
```

### 2. Batch Fixing After Initial Scan

**Scenario:** Just ran `a11y-guard check` and found many violations.

```bash
# Initial scan
a11y-guard check src/components/

# Found violations in 12 files
# Fix them one by one in interactive mode

for file in src/components/*.tsx; do
  echo "Fixing $file..."
  a11y-guard fix "$file"
done
```

### 3. Learning Mode for New Team Members

**Scenario:** Teaching accessibility to junior developers.

```bash
# Use interactive mode (default) with full explanations
a11y-guard fix src/components/Form.tsx

# Junior developer sees:
# - The violation
# - Why it's a problem
# - WCAG criteria
# - How to fix it
# - Educational resources

# They learn while fixing!
```

### 4. CI/CD Suggested Fixes

**Scenario:** Show fix suggestions as output.

```bash
# Show suggestions without applying
a11y-guard fix src/components/Form.tsx --no-interactive > fix-suggestions.md
```

## Troubleshooting

### Issue: "No violations found" but check shows violations

**Cause:** Fix command only handles violations it can automatically fix.

**Solution:**
```bash
# Check which violations were found
a11y-guard check

# Some violations require manual fixes (e.g., semantic issues)
# Fix command will skip these with a note
```

### Issue: Fix applied but syntax error introduced

**Cause:** Rare AI generation issue or complex code structure.

**Solution:**
```bash
# File is auto-backed up before fix
cp src/file.tsx.backup src/file.tsx

# Try with different approach or manual fix
# Report the issue if it persists
```

### Issue: Low confidence scores

**Cause:** Insufficient training data in knowledge base.

**Solution:**
```bash
# Build knowledge base from git history
a11y-guard learn --full

# This gives the AI more context
# Then retry fix command
a11y-guard fix src/file.tsx
```

### Issue: Fixes don't match team coding style

**Cause:** AI hasn't learned team patterns yet.

**Solution:**
```bash
# Apply fixes manually the way your team prefers
# Then commit

# A11yGuard will learn from your commits
git add src/file.tsx
git commit -m "fix: accessibility violations following team style"

# After a few commits, AI adapts to your style
a11y-guard learn --incremental
```

### Issue: "Rate limit exceeded" with AI API

**Cause:** Too many API calls in short time.

**Solution:**
```bash
# Wait a few minutes

# Or fix files one at a time with delays
for file in *.tsx; do
  a11y-guard fix "$file"
  sleep 10
done
```

### Issue: Want to undo applied fix

**Cause:** Applied fix but changed mind.

**Solution:**
```bash
# Before saving, use git
git diff src/file.tsx  # Review changes
git checkout src/file.tsx  # Undo

# After saving
git add src/file.tsx
git commit -m "Revert accessibility fix"

# A11yGuard will learn this preference
```

## Related Commands

- [check](./check.md) - Scan for violations before fixing
- [ask](./ask.md) - Get AI help understanding accessibility issues
- [analyze](./analyze.md) - See patterns in successful fixes
- [learn](./learn.md) - Improve fix accuracy by learning from git history

## Additional Notes

### Privacy & Security

- All AI processing uses your API keys
- Code is sent to AI provider (OpenAI/Anthropic)
- No code is stored by A11yGuard on external servers
- Knowledge base stays local (.a11y-guard/knowledge.db)

### Success Rate Tracking

Every fix application is tracked:
- ✅ Applied: Fix was applied
- ❌ Rejected: User declined fix
- 📊 Verified: Subsequent commit kept the fix
- 🔄 Reverted: Subsequent commit removed the fix

This feedback improves future suggestions.

### Keyboard Shortcuts (Interactive Mode)

- `y` - Apply fix
- `n` - Skip and record rejection
- `s` - Skip without recording
- `q` - Quit (stop showing fixes)
- `d` - Show detailed explanation
- `?` - Show help
