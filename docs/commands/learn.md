# learn

Build team accessibility knowledge by learning from git commit history.

## Syntax

```bash
a11y-guard learn [options]
```

## Description

The `learn` command analyzes your project's git history to automatically extract accessibility fix patterns. It examines commits where accessibility violations were fixed, infers the patterns used, and stores them in the knowledge base.

This learned knowledge powers:
- **Smart scanning** - Predicts likely violations based on code patterns
- **Fix suggestions** - More accurate AI-generated fixes
- **Team patterns** - Recommendations aligned with your coding style
- **Historical context** - Understanding why certain approaches work for your team

The learning process uses 8 different pattern detection algorithms to identify common fix types like adding aria-labels, alt text, form labels, keyboard handlers, and more.

By default, `learn` runs incrementally, processing only new commits since the last learning session. This makes it fast enough to run automatically via the post-merge git hook.

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--commits` | number | `100` | Maximum number of commits to analyze |
| `--incremental` | boolean | `true` | Only process new commits since last learn |
| `--no-incremental` | boolean | false | Disable incremental learning |
| `--full` | boolean | false | Full re-learn: ignore last sync and re-analyze all commits |
| `--silent` | boolean | false | Silent mode: minimal output for git hooks |
| `--branch` | string | current | Branch to learn from |

## Examples

### Basic Incremental Learning

```bash
a11y-guard learn
```

**Actual Output:**
```
✔ Knowledge base is up-to-date

✅ No new commits to learn from
New commits: 12

Analyzing commits...
Progress: [████████████████████] 100% (12/12) 

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📚 Learning Summary
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Commits analyzed: 12
Files modified: 34
Patterns detected: 8

Patterns learned:

1. Add aria-label attribute
   Occurrences: 5
   Example: <button> → <button aria-label="Close">
   Files: Modal.tsx, Dialog.tsx, IconButton.tsx
   
2. Add alt text to images
   Occurrences: 3
   Example: <img src={...} /> → <img src={...} alt="..." />
   Files: ImageGallery.tsx, ProductCard.tsx
   
3. Link label to input
   Occurrences: 4
   Example: Added htmlFor and id attributes
   Files: Form.tsx, LoginForm.tsx

4. Add keyboard event handler
   Occurrences: 2
   Example: onClick → onClick + onKeyPress
   Files: Card.tsx

Total patterns in KB: 67 (↑ 8 from last session)
Knowledge base updated successfully!

💡 This improved knowledge will make fix suggestions more accurate
```

### Full History Learning (First Time Setup)

```bash
a11y-guard learn --full
```

**Expected Output:**
```
🧠 Learning from complete git history...

⚠️  Full mode will reprocess entire history
   This may take a few minutes for large repositories

Branch: main
Total commits: 1,247
Starting from: Initial commit (2023-03-15)

Analyzing commits...
Progress: [████████----------] 45% (561/1247)
ETA: 2 minutes

[... continues ...]

Progress: [████████████████████] 100% (1247/1247)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📚 Learning Summary
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Commits analyzed: 1,247
Files modified: 3,842
A11y-related commits: 178 (14%)
Patterns detected: 234

Pattern Breakdown:

1. aria-label additions: 67 occurrences
   Success rate: 96%
   Most common in: Button components
   
2. alt text additions: 54 occurrences
   Success rate: 94%
   Most common in: Image components
   
3. form label associations: 42 occurrences
   Success rate: 100%
   Most common in: Form components
   
4. keyboard event handlers: 31 occurrences
   Success rate: 88%
   Most common in: Interactive divs
   
5. ARIA role additions: 18 occurrences
   Success rate: 91%
   Most common in: Custom components
   
6. tabIndex modifications: 12 occurrences
   Success rate: 85%
   Most common in: Modal components
   
7. heading hierarchy fixes: 6 occurrences
   Success rate: 100%
   Most common in: Page layouts
   
8. autocomplete attributes: 4 occurrences
   Success rate: 100%
   Most common in: Form inputs

Time period: 2023-03-15 to 2026-01-30
Authors: 8 contributors
Knowledge base built successfully!

💡 Run 'a11y-guard analyze' to see detailed patterns
```

### Silent Mode (For Git Hooks)

```bash
a11y-guard learn --incremental --silent
```

**Expected Output:**
```
(no output, runs in background)
```

**Exit Codes:**
- `0`: Success
- `1`: Error (git not available, etc.)



## How It Works

### Learning Pipeline

The learn command analyzes git history through a 4-stage pipeline:

**1. Commit Discovery Phase**

Finds accessibility-related commits by scanning commit messages for keywords:

```typescript
const A11Y_KEYWORDS = [
  'a11y', 'accessibility', 'aria', 'wcag', 'screen reader',
  'keyboard', 'focus', 'alt text', 'label', 'semantic',
  'contrast', 'tabindex', 'role', 'landmark'
];

// Only analyze commits likely related to accessibility
const a11yCommits = allCommits.filter(commit => 
  A11Y_KEYWORDS.some(keyword => 
    commit.message.toLowerCase().includes(keyword)
  )
);
```

**2. Diff Analysis Phase**

For each relevant commit, extracts before/after code:

```typescript
async function analyzeDiff(commit) {
  const diff = await git.show(commit.hash);
  
  for (const file of diff.files) {
    const before = extractLines(diff, file, 'before');
    const after = extractLines(diff, file, 'after');
    
    // Run 8 pattern detectors
    const patterns = detectAllPatterns(before, after);
    
    // Store in knowledge base
    for (const pattern of patterns) {
      await kb.recordPattern(pattern, commit);
    }
  }
}
```

**3. Pattern Detection Phase** - 8 Specialized Detectors

**Detector 1: ARIA Labels** (95% confidence)
```javascript
// Detects: <button> → <button aria-label="Close">
Pattern: Adding aria-label to elements without text content
Examples:
  <Icon name="x" /> → <Icon name="x" aria-label="Delete" />
  <button onClick={...}> → <button aria-label="Submit" onClick={...}>
```

**Detector 2: Alt Text** (98% confidence)
```javascript
// Detects: <img src={...} /> → <img src={...} alt="..." />
Pattern: Adding alt attributes to images
Examples:
  <img src={avatar} /> → <img src={avatar} alt={user.name} />
  <img src="/logo.png" /> → <img src="/logo.png" alt="Company Logo" />
```

**Detector 3: Form Labels** (92% confidence)
```javascript
// Detects: label + input linking via htmlFor/id
Pattern: Associating labels with form controls
Examples:
  Added: <label htmlFor="email">Email</label>
  Added: <input id="email" /> to match existing label
```

**Detector 4: Keyboard Handlers** (87% confidence)
```javascript
// Detects: onClick → onClick + onKeyPress
Pattern: Adding keyboard event handlers to clickable elements
Examples:
  <div onClick={...}> → <div onClick={...} onKeyPress={...} role="button">
  Added: tabIndex={0} for keyboard focus
```

**Detector 5: ARIA Roles** (90% confidence)
```javascript
// Detects: role attribute additions
Pattern: Adding semantic roles to generic elements
Examples:
  <div> → <div role="button">
  <ul> → <ul role="navigation">
```

**Detector 6: TabIndex** (85% confidence)
```javascript
// Detects: tabIndex additions for focus management
Pattern: Making elements keyboard-focusable
Examples:
  <div> → <div tabIndex={0}>
  <div tabIndex="-1"> → <div tabIndex={0}>
```

**Detector 7: Heading Hierarchy** (93% confidence)
```javascript
// Detects: heading level corrections
Pattern: Fixing heading structure for proper document outline
Examples:
  <h1> ... <h3> → <h1> ... <h2>
  Multiple <h1> → Single <h1>, others <h2>
```

**Detector 8: Autocomplete** (96% confidence)
```javascript
// Detects: autocomplete attribute additions
Pattern: Adding autocomplete for better UX and accessibility
Examples:
  <input type="email"> → <input type="email" autocomplete="email">
  <input type="password"> → <input type="password" autocomplete="current-password">
```

**4. Knowledge Base Update Phase**

Patterns stored with rich metadata for future use:

```sql
-- patterns table: aggregated statistics
CREATE TABLE patterns (
  id INTEGER PRIMARY KEY,
  pattern_name TEXT,              -- 'aria-label-addition'
  description TEXT,               -- Human-readable description
  occurrences INTEGER,            -- Times seen: 67
  fixes_applied INTEGER,          -- Times successfully fixed: 64
  fix_success_rate REAL,          -- 64/67 = 95.5%
  common_fix TEXT,                -- Most frequent transformation
  risk_score REAL,                -- Confidence in pattern: 0.95
  first_seen TIMESTAMP,
  last_seen TIMESTAMP
);

-- Fix examples for learning
CREATE TABLE fix_examples (
  id INTEGER PRIMARY KEY,
  pattern_id INTEGER,
  file_path TEXT,
  commit_hash TEXT,
  before_code TEXT,
  after_code TEXT,
  confidence REAL,
  author TEXT
);
```

### Incremental Learning

Tracks progress to avoid reprocessing:

```sql
-- Stores last processed commit
CREATE TABLE kb_metadata (
  key TEXT PRIMARY KEY,
  value TEXT,                     -- 'a3f29b2' (commit hash)
  updated_at TIMESTAMP
);

-- Check on next run
SELECT value FROM kb_metadata WHERE key = 'last_learned_commit';
-- Returns: 'a3f29b2'

-- Only process commits after this point
git log a3f29b2..HEAD
```

### How Patterns Improve Fixes

When generating fixes, patterns guide the AI:

```typescript
async function generateFix(violation) {
  // 1. Find similar historical violations
  const similar = await kb.getSimilarViolations(
    violation.filePath,
    violation.violationType,
    limit: 5
  );
  
  // 2. Get pattern statistics
  const pattern = await kb.getPatternStats(violation.violationType);
  // Returns: { 
  //   fixSuccessRate: 0.955,
  //   occurrences: 67,
  //   commonFix: '<button aria-label="...">'
  // }
  
  // 3. Decision tree
  if (pattern && pattern.fixSuccessRate > 0.8) {
    // HIGH CONFIDENCE: Use learned pattern
    console.log(`Using team pattern (${pattern.fixSuccessRate * 100}% success)`);
    return adaptPattern(pattern.commonFix, violation.context);
  } 
  else if (similar.length >= 3) {
    // MEDIUM CONFIDENCE: Similar cases exist
    console.log(`Found ${similar.length} similar cases, adapting...`);
    return await aiAgent.generateFromExamples(violation, similar);
  }
  else {
    // LOW CONFIDENCE: Novel violation
    console.log('New violation type, using AI generation...');
    return await aiAgent.generateFix(violation);
  }
}
```

### Git History Analysis

```typescript
// Learning process
async function learn(options: LearnOptions) {
  // 1. Get commits to analyze
  const commits = await getCommits({
    branch: options.branch,
    since: getLastLearnedCommit(),
    limit: options.commits
  });
  
  // 2. For each commit, analyze diff
  for (const commit of commits) {
    const diff = await git.show(commit.hash);
    const fixes = await inferFixes(diff);
    
    // 3. Detect patterns
    for (const fix of fixes) {
      const pattern = detectPattern(fix);
      if (pattern) {
        await savePattern(pattern);
      }
    }
  }
  
  // 4. Update metadata
  await updateLastLearnedCommit(commits[0].hash);
}
```

### 8 Pattern Detection Algorithms

```typescript
// Pattern detectors
const PATTERN_DETECTORS = [
  // 1. ARIA Label Pattern
  {
    name: 'aria-label',
    detect: (before, after) => {
      const beforeAST = parse(before);
      const afterAST = parse(after);
      
      // Check if aria-label was added
      const added = findAddedAttributes(beforeAST, afterAST)
        .filter(attr => attr.name === 'aria-label');
      
      if (added.length > 0) {
        return {
          type: 'aria-label-addition',
          context: extractContext(after),
          confidence: 0.95
        };
      }
    }
  },
  
  // 2. Alt Text Pattern
  {
    name: 'alt-text',
    detect: (before, after) => {
      // Detect <img> tags gaining alt attribute
      const imgPattern = /<img[^>]*src=["'][^"']+["'][^>]*>/g;
      const beforeImgs = before.match(imgPattern) || [];
      const afterImgs = after.match(imgPattern) || [];
      
      const addedAlt = afterImgs.filter(img => 
        img.includes('alt=') && 
        !beforeImgs.some(b => b === img)
      );
      
      if (addedAlt.length > 0) {
        return {
          type: 'alt-text-addition',
          examples: addedAlt,
          confidence: 0.98
        };
      }
    }
  },
  
  // 3. Form Label Pattern
  {
    name: 'form-labels',
    detect: (before, after) => {
      // Detect htmlFor + id linking
      const htmlForPattern = /htmlFor=["']([^"']+)["']/;
      const idPattern = /id=["']([^"']+)["']/;
      
      const beforeIds = extractIds(before);
      const afterIds = extractIds(after);
      const newLinks = afterIds.filter(id => !beforeIds.includes(id));
      
      if (newLinks.length > 0) {
        return {
          type: 'label-input-link',
          linkedIds: newLinks,
          confidence: 0.92
        };
      }
    }
  },
  
  // 4. Keyboard Handler Pattern
  {
    name: 'keyboard-handlers',
    detect: (before, after) => {
      const keyboardEvents = ['onKeyPress', 'onKeyDown', 'onKeyUp'];
      
      const addedHandlers = keyboardEvents.filter(event =>
        !before.includes(event) && after.includes(event)
      );
      
      if (addedHandlers.length > 0) {
        return {
          type: 'keyboard-handler-addition',
          handlers: addedHandlers,
          confidence: 0.87
        };
      }
    }
  },
  
  // 5-8: Similar for ARIA roles, tabIndex, headings, autocomplete
];
```

### Pattern Storage

```typescript
// How patterns are stored in KB
interface FixPattern {
  id: string;
  type: string;  // 'aria-label', 'alt-text', etc.
  context: {
    componentType: string;  // 'button', 'img', etc.
    surrounding: string;    // code context
  };
  transformation: {
    before: string;
    after: string;
    attributes: string[];
  };
  metadata: {
    commits: string[];      // commits where used
    files: string[];        // files affected
    authors: string[];      // who applied it
    successRate: number;    // how often it worked
    confidence: number;     // detection confidence
  };
  timestamps: {
    firstSeen: Date;
    lastSeen: Date;
    timesApplied: number;
  };
}
```

### Incremental Learning Tracking

```sql
-- How last learned commit is tracked
CREATE TABLE kb_metadata (
  key TEXT PRIMARY KEY,
  value TEXT,
  updated_at TIMESTAMP
);

INSERT INTO kb_metadata VALUES (
  'last_learned_commit',
  'a3f29b2c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9',
  '2026-01-30 14:30:00'
);

-- Next incremental run starts from this commit
```

## Common Use Cases

### 1. Initial Setup (Building Knowledge Base)

**Scenario:** Just initialized A11yGuard, need to build knowledge.

```bash
# After a11y-guard init
a11y-guard learn --full --commits 1000

# This processes up to 1000 commits
# Takes 2-5 minutes depending on repo size
# One-time operation

# Verify knowledge was built
a11y-guard analyze
```

### 2. Post-Merge Automatic Learning (Git Hook)

**Scenario:** Automatically learn from new commits after merge/pull.

```bash
# In .git/hooks/post-merge (installed by init)
#!/bin/sh
a11y-guard learn --incremental --silent

# Runs after:
# - git pull
# - git merge
# - git rebase

# Fast (< 1 second for typical merges)
# Silent (no output)
```

### 3. Team Knowledge Refresh

**Scenario:** Weekly refresh to catch all team members' commits.

```bash
# Manual weekly refresh
a11y-guard learn --commits 100

# Or automated via cron
0 9 * * 1 cd /path/to/project && a11y-guard learn --commits 100

# Keeps knowledge base fresh with team's latest patterns
```

### 4. Onboarding New Team Member

**Scenario:** Help new member learn team's accessibility patterns.

```bash
# Generate learning report for new member
a11y-guard learn --full --commits 500 > team-a11y-patterns.txt

# Shows them:
# - Common accessibility issues team has fixed
# - Preferred fix approaches
# - Team coding standards
```

### 5. Debugging Poor Fix Suggestions

**Scenario:** Fix command suggests wrong patterns.

**Solution:**
```bash
# Check what was learned
a11y-guard analyze

# If patterns look wrong, relearn from scratch
a11y-guard learn --full
```

## Troubleshooting

### Issue: "Not a git repository"

**Cause:** Running outside a git repo.

**Solution:**
```bash
# Check if in git repo
git status

# Initialize git if needed
git init

# Then learn
a11y-guard learn
```

### Issue: "No commits found"

**Cause:** Repository has no commit history yet.

**Solution:**
```bash
# Make some commits first
git add .
git commit -m "Initial commit"

# Learn will work once there are commits
# For now, knowledge base will be built from scans
a11y-guard check src/
```

### Issue: Learning is very slow

**Cause:** Analyzing too many commits or large files.

**Solution:**
```bash
# Reduce commit count
a11y-guard learn --commits 50
```

### Issue: Patterns not detected

**Cause:** Commits don't contain recognizable accessibility fixes.

**Solution:**
```bash
# Check what commits were analyzed
git log --oneline -n 20

# Look for commits with a11y-related changes
git log --grep="accessibility\|a11y\|aria\|wcag"

# If team doesn't use a11y keywords:
# Learn will detect patterns from diffs
# Just needs more commits with actual fixes

# Manual fix → commit → learn cycle
a11y-guard fix src/Button.tsx
# Apply fixes
git add src/Button.tsx
git commit -m "fix: button accessibility"
a11y-guard learn --incremental
```

### Issue: "Last learned commit not found"

**Cause:** Commit was deleted/rebased or KB corrupted.

**Solution:**
```bash
# Reset and relearn
a11y-guard learn --full

# This clears last_learned_commit and starts fresh
```

### Issue: Learning wrong patterns from bad commits

**Cause:** Some commits introduced accessibility issues rather than fixes.

**Solution:**
```bash
# Relearn from a time period when quality was good
a11y-guard learn --full

# Knowledge base will be rebuilt from all commits
```

## Related Commands

- [init](./init.md) - Sets up post-merge hook that runs learn automatically
- [analyze](./analyze.md) - View learned patterns and their effectiveness
- [fix](./fix.md) - Uses learned patterns to generate better suggestions
- [check](./check.md) - Smart mode uses learned patterns for predictions

## Additional Notes

### Performance

- **Incremental mode**: < 1 second for typical merges (1-10 commits)
- **Full mode**: 1-5 minutes for typical repos (100-1000 commits)
- **Large repos**: Use `--commits` to limit (recommended: 500-1000)

### Pattern Confidence

Patterns are scored by confidence:
- **95-100%**: Direct attribute addition (aria-label, alt)
- **90-95%**: Structural changes (label-input linking)
- **85-90%**: Behavioral changes (keyboard handlers)
- **80-85%**: Complex changes (role modifications)

Low confidence patterns (<80%) are not used for automatic suggestions.

### What Gets Learned

✅ **Learned:**
- Added attributes (aria-*, alt, htmlFor, etc.)
- Added event handlers (onKeyPress, etc.)
- Element conversions (div → button)
- Attribute modifications (role changes)
- Heading hierarchy fixes
- Autocomplete additions

❌ **Not Learned:**
- Deleted code
- Refactoring without a11y changes
- Style/CSS changes
- Comment changes
- Configuration changes

### Git Hook Integration

The post-merge hook installed by `init` runs:
```bash
#!/bin/sh
# A11yGuard post-merge hook
a11y-guard learn --incremental --silent
```

This ensures knowledge base stays updated automatically.

### Multi-Branch Learning

```bash
# Learn from different branches
a11y-guard learn --branch main
a11y-guard learn --branch develop
a11y-guard learn --branch feature/accessibility

# All patterns merge into single KB
# Deduplicated automatically
```

### Knowledge Base Size

Typical KB sizes:
- **Small project** (10 components): 20-50 patterns
- **Medium project** (50 components): 100-200 patterns
- **Large project** (200+ components): 300-500 patterns

Database size: ~1-5 MB for most projects

### Privacy & Security

- All learning is local (no external API calls)
- No data sent outside your machine
- Commit history stays private
- Knowledge base is project-specific

### Best Practices

1. **Run full learn once** after init
2. **Let post-merge hook** handle incremental updates
3. **Weekly manual refresh** for active teams: `a11y-guard learn --commits 100`
4. **Relearn after major refactors** to update patterns

### Integration with Fix Command

The more patterns learned, the better fix suggestions become:

| Patterns in KB | Fix Quality |
|----------------|-------------|
| 0-10 | Generic suggestions |
| 10-50 | Team-aware suggestions |
| 50-100 | High-quality, style-matched |
| 100+ | Excellent, context-aware |

Aim for 50+ patterns for best results.
