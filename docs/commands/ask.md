# ask

Get AI-powered answers to accessibility questions with context from your team's knowledge base.

## Syntax

```bash
a11y-guard ask "<question>"
```

## Description

The `ask` command provides an AI-powered Q&A interface for accessibility questions. It combines:
- General WCAG 2.1 and ARIA knowledge
- Your team's specific patterns from the knowledge base
- Context about your codebase and common issues
- Code examples tailored to your project

This makes it perfect for learning accessibility, getting quick guidance, and understanding why certain patterns are recommended for your specific project.

Unlike generic ChatGPT, `ask` is aware of your team's accessibility history, coding patterns, and common challenges.

## Options

The `ask` command currently takes no options. Simply provide your question as an argument.

## Examples

### Basic Question

```bash
a11y-guard ask "How do I make a button accessible?"
```

**Actual Output:**
```
⠴ Thinking...
🤖 AI-Powered Q&A
Using: GitHub Copilot (Model: claude-sonnet-4.5)


🎓 A11yGuard Educator:

To make a button accessible, follow these best practices:

## 1. **Use Semantic HTML**
<!-- ✅ Good -->
<button>Submit</button>

<!-- ❌ Avoid -->
<div onclick="submit()">Submit</div>

## 2. **Provide Clear Labels**
- Use descriptive text (avoid "Click here" or "Submit")
- Make the purpose obvious:
<button>Add to Cart</button>
<button>Delete Product</button>

## 3. **Ensure Keyboard Accessibility**
- Native `<button>` elements work automatically (Space/Enter)
- If you must use other elements, add `role="button"` and `tabindex="0"`

## 4. **Visible Focus States**
button:focus {
  outline: 2px solid blue;
  outline-offset: 2px;
}

## 5. **Adequate Size & Spacing**
- Minimum 44×44 pixels touch target (WCAG 2.5.5)
- Space buttons to prevent mis-clicks

## 6. **Color Contrast**
- Text-to-background ratio of at least 4.5:1 (WCAG 1.4.3)

## 7. **Icon-Only Buttons Need Labels**
<button aria-label="Close dialog">
  <span aria-hidden="true">×</span>
</button>

## Why It Matters
- **Screen reader users** need clear labels to understand purpose
- **Keyboard users** need focus indicators to navigate
- **Motor impairment users** need larger touch targets
- **Low vision users** need sufficient contrast

Would you like me to review any specific button code you're working with?

💰 Token Usage
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Input tokens:  10690
Output tokens: 566
Cache tokens:  5215
Total tokens:  11256
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━


💡 Follow-up? Just run "a11y-guard ask" with your next question.
```

### WCAG Compliance Question

```bash
a11y-guard ask "What are WCAG 2.1 Level AA requirements?"
```

**Actual Output:**
```
⠴ Thinking...
🤖 AI-Powered Q&A
Using: GitHub Copilot (Model: claude-sonnet-4.5)


🎓 A11yGuard Educator:

WCAG 2.1 Level AA represents the **recommended standard** for web accessibility - it's what most organizations aim for and what many laws require (like the ADA, Section 508, and European accessibility directives).

## What Level AA Means

WCAG has three conformance levels:
- **Level A**: Minimum (if you don't meet this, some users can't access content at all)
- **Level AA**: Recommended standard (addresses major barriers)
- **Level AAA**: Enhanced (highest level, not always possible for all content)

## Key Level AA Requirements

**Visual & Color:**
- Minimum contrast ratio of **4.5:1** for normal text, 3:1 for large text
- Don't rely on color alone to convey information

**Interaction:**
- All functionality available via keyboard
- No keyboard traps
- Visible focus indicators
- Sufficient time to read/use content (with ability to extend, adjust, or disable time limits)

**Navigation:**
- Multiple ways to find pages (search, sitemap, navigation)
- Descriptive headings and labels
- Clear focus order

**Forms & Errors:**
- Labels or instructions for user input
- Error identification and suggestions
- Error prevention for legal/financial transactions

**Content:**
- Page titles that describe topic/purpose
- Link text that makes sense out of context
- Consistent navigation and identification

**WCAG 2.1 Additions** (beyond 2.0):
- Orientation support (portrait/landscape)
- Reflow at 400% zoom without horizontal scrolling
- Touch target size (minimum 44×44 CSS pixels)
- Support for text spacing adjustments

Would you like me to explain any specific criterion in more detail?

💰 Token Usage
────────────────────────────────────────────────────────────────
Input tokens:  10693
Output tokens: 527
Cache tokens:  5215
Total tokens:  11220
────────────────────────────────────────────────────────────────


💡 Follow-up? Just run "a11y-guard ask" with your next question.
```

### Debugging Specific Error

```bash
a11y-guard ask "Why am I getting jsx-a11y/label-has-associated-control?"
```

**Actual Output:**
```
⠦ Thinking...
🤖 AI-Powered Q&A
Using: GitHub Copilot (Model: claude-sonnet-4.5)


🎓 A11yGuard Educator:

Perfect! I found the issues. The **jsx-a11y/label-has-associated-control** rule is flagging your LoginForm component because your form inputs aren't properly connected to their labels.

## Why This Matters 🎯

**For screen reader users:** When a label isn't associated with its input, the screen reader can't tell the user what information to enter. They just hear "edit text, blank" with no context. Imagine filling out a form blindfolded with no hints about what goes where!

**For everyone else:** Clicking a properly associated label focuses its input, making forms easier to use (bigger click target).

## The Three Violations in Your Code:

1. **Lines 19-25** - Input with NO label at all (just placeholder text, which screen readers may not announce)
2. **Lines 30-37** - Using a `<div>` instead of a `<label>` element
3. **Lines 41-43** - Label exists but isn't connected to the input

## How to Fix ✅

**Option 1 - Wrap input with label:**
<label>
  Email
  <input type="email" />
</label>

**Option 2 - Use htmlFor/id association:**
<label htmlFor="email">Email</label>
<input id="email" type="email" />

Both tell screen readers: "This label describes this input." The form becomes fully navigable and understandable without vision!

Would you like me to show you the corrected version of your LoginForm?

💰 Token Usage
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Input tokens:  12239
Output tokens: 360
Cache tokens:  11296
Total tokens:  12599
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━


💡 Follow-up? Just run "a11y-guard ask" with your next question.
```

### Compare Approaches

```bash
a11y-guard ask "Should I use aria-label or visually hidden text?"
```

**Expected Output:**
```
🤖 A11yGuard AI Assistant

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

aria-label vs Visually Hidden Text

Both provide accessible names, but have different use cases:

aria-label
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Use when:
  • Icon-only buttons
  • Providing extra context not visible
  • Overriding visible text for better accessibility

Example:
<button aria-label="Close dialog">
  <X />  {/* Icon only */}
</button>

⚠️  Downsides:
  • Not translated by browser translation tools
  • Not visible in code review
  • Doesn't help non-screen-reader users

Visually Hidden Text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Use when:
  • Providing extra context for screen readers
  • Supplementing visual-only information
  • Supporting internationalization

Example:
<button>
  <span className="sr-only">Close dialog</span>
  <X />
</button>

/* CSS for sr-only */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

✅ Benefits:
  • Gets translated by browser tools
  • Visible in code
  • Helps with SEO

Your team's pattern:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Based on your knowledge base:
  • aria-label: Used 42 times (mostly icon buttons)
  • sr-only: Used 18 times (supplemental info)

Recommendation for your project:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Since you're working on an internationalized app (detected i18n setup),
prefer visually hidden text for better translation support.

Example from your Button.tsx:
<button>
  <span className="sr-only">{t('close')}</span>
  <Icon name="close" />
</button>

This way translations apply to screen reader text too.
```

### Project-Specific Pattern Question

```bash
a11y-guard ask "What are our most common accessibility issues?"
```

**Expected Output:**
```
🤖 Analyzing your project's knowledge base...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Your Team's Most Common Accessibility Issues

Based on 234 violations in your knowledge base:

1. Missing Alt Text on Images (42 occurrences)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Where: Mostly in ImageGallery.tsx and ProductCard.tsx
Pattern: Images in .map() loops often missing alt
Your fix rate: 90% (38/42 fixed)

Common pattern in your code:
{images.map(img => <img src={img.url} />)}

Your team's fix:
{images.map(img => <img src={img.url} alt={img.description || 'Product image'} />)}

2. ARIA Props Errors (38 occurrences)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Where: Modal.tsx, Dialog components
Pattern: Using invalid ARIA attributes or wrong values
Your fix rate: 92% (35/38 fixed)

Most common mistake:
<div role="button" aria-pressed="true">  // Wrong
<button aria-pressed={true}>  // Correct

3. Missing Form Labels (31 occurrences)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Where: Form.tsx, LoginForm.tsx, SearchBox.tsx
Pattern: <input> without associated <label>
Your fix rate: 94% (29/31 fixed)

Your team's preferred pattern:
<label htmlFor="email">Email</label>
<input id="email" type="email" />

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Why these patterns?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Analysis of your codebase shows:
  • Heavy use of dynamic data (maps, filters)
  • Many reusable components with icons
  • Form-heavy application

Recommendations:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Create a reusable Image component that requires alt prop:
   <Image src={url} alt={description} />

2. Add ESLint rule to enforce form labels:
   'jsx-a11y/label-has-associated-control': 'error'

3. Use TypeScript to enforce ARIA props:
   type ButtonProps = { 'aria-label': string; ... }

Run 'a11y-guard analyze' for detailed breakdown
```

## How It Works

### AI Context Assembly

```typescript
// How the ask command builds context
async function answerQuestion(question: string) {
  // 1. Load team's knowledge base
  const kb = await loadKnowledgeBase();
  const violations = await kb.getViolations();
  const fixes = await kb.getFixes();
  const patterns = await kb.getPatterns();
  
  // 2. Extract relevant context
  const relevantViolations = findRelevant(violations, question);
  const relevantFixes = findRelevant(fixes, question);
  
  // 3. Build prompt with context
  const prompt = `
    Question: ${question}
    
    Context about user's codebase:
    - Total violations: ${violations.length}
    - Common issues: ${patterns.topIssues}
    - Team patterns: ${patterns.fixPatterns}
    - Recent fixes: ${relevantFixes}
    
    Provide answer that:
    1. Addresses the question
    2. References user's specific patterns
    3. Includes code examples from their project
    4. Cites WCAG criteria
    5. Suggests next actions with a11y-guard
  `;
  
  // 4. Query AI
  const answer = await queryAI(prompt);
  
  return formatAnswer(answer);
}
```

### Semantic Search for Relevance

```typescript
// Find relevant knowledge base entries
function findRelevant(entries, question) {
  // Use embeddings for semantic similarity
  const questionEmbedding = getEmbedding(question);
  
  return entries
    .map(entry => ({
      entry,
      similarity: cosineSimilarity(
        questionEmbedding,
        getEmbedding(entry.description)
      )
    }))
    .filter(item => item.similarity > 0.7)
    .sort((a, b) => b.similarity - a.similarity)
    .slice(0, 5)
    .map(item => item.entry);
}
```

## Common Use Cases

### 1. Learning Accessibility

**Scenario:** New team member learning accessibility.

```bash
# Start with basics
a11y-guard ask "What is WCAG?"

# Learn about specific topics
a11y-guard ask "How do screen readers work?"

# Understand team patterns
a11y-guard ask "What accessibility patterns does our team follow?"

# Get help with specific tasks
a11y-guard ask "How do I make this modal accessible?"
```

### 2. Debugging Failures

**Scenario:** Pre-commit hook blocked your commit.

```bash
# Commit failed with:
# ❌ jsx-a11y/img-redundant-alt in ImageCard.tsx:23

# Ask for help
a11y-guard ask "Why am I getting jsx-a11y/img-redundant-alt?"

# Get explanation and fix
# Then apply the fix
```

### 3. Code Review Guidance

**Scenario:** Reviewing a PR with accessibility changes.

```bash
# Reviewer unsure about approach
a11y-guard ask "Is it better to use aria-describedby or aria-labelledby?"

# Get project-specific recommendation
# Leave informed feedback
```

### 4. Architecture Decisions

**Scenario:** Designing a new component library.

```bash
# Gather accessibility requirements
a11y-guard ask "What accessibility features should a modal component have?"

# Get comprehensive checklist
# Use to write component spec
```

## Troubleshooting

### Issue: Generic answers, not project-specific

**Cause:** Knowledge base is empty or sparse.

**Solution:**
```bash
# Build knowledge from git history
a11y-guard learn --full

# Run some scans
a11y-guard check src/

# Then ask again - now has context
a11y-guard ask "What are our common issues?"
```

### Issue: "I don't have enough context to answer"

**Cause:** Question too vague or requires specific code context.

**Solution:**
```bash
# Be more specific
a11y-guard ask "How do I add aria-label to the close button in Modal.tsx line 45?"
```

### Issue: Answer references outdated patterns

**Cause:** Knowledge base has old data, team patterns changed.

**Solution:**
```bash
# Refresh knowledge base
a11y-guard learn --incremental

# This updates with latest commits and patterns
```

## Related Commands

- [check](./check.md) - Scan to populate knowledge base
- [fix](./fix.md) - Get automated fix suggestions
- [analyze](./analyze.md) - See patterns that inform ask responses
- [learn](./learn.md) - Build knowledge base for better context

## Additional Notes

### AI Models

The ask command uses:
- GPT-4 Turbo for general questions
- Your knowledge base for project-specific context
- Semantic search for relevant examples

### Privacy

- Questions and answers are sent to OpenAI API
- Your knowledge base context is included
- No data stored externally by A11yGuard
- Use your own API key

### Accuracy

The AI provides:
- ✅ Accurate WCAG references
- ✅ Valid code examples
- ⚠️ Suggestions (review before applying)

Always verify important decisions.

### Question Tips

For best results:
- Be specific: "How do I..." vs "Tell me about..."
- Include details: Reference file names or line numbers when relevant
- Follow up: Ask clarifying questions

### Integration Ideas

```bash
# VS Code integration
# Add to tasks.json
{
  "label": "Ask A11yGuard",
  "type": "shell",
  "command": "a11y-guard ask '${input:question}'"
}

# Slack bot
curl -X POST webhook-url -d \
  "$(a11y-guard ask \"${USER_QUESTION}\")"

# Save answers for reference
for q in "${questions[@]}"; do
  echo "## $q" >> docs/faq.md
  a11y-guard ask "$q" >> docs/faq.md
done
```
