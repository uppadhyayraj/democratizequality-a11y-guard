# Democratize-Quality A11yGuard 🛡️

**AI-Powered Accessibility Guardian for Modern Development by Democratize-Quality**

> *"Shift accessibility left - catch issues during development, not after deployment."*

---

## 📖 The Story

Imagine: It's Friday afternoon. Your team just shipped a beautiful new feature. The design is pixel-perfect, the animations are smooth, and everyone's celebrating. Then Monday morning arrives with a support ticket: *"I can't use your app with my screen reader."* Your heart sinks. Accessibility wasn't an afterthought - you even ran Lighthouse and axe-core during QA. But those tools only caught what was already built. The damage was done.

**This is the accessibility gap** that A11yGuard was built to close.

### The Problem with Current Tools

We have amazing tools like **Lighthouse**, **axe-core**, and **WAVE** - they're excellent at finding accessibility issues. But they all share the same limitation: **they test after you build**. By the time you run them:

- ✨ Your feature is "complete"
- 🎨 Design decisions are finalized  
- 🏗️ Code is written and reviewed
- 😰 Fixing issues means rework, delays, and context-switching

It's like having a spell-checker that only runs after you've published your book.

### The A11yGuard Difference

A11yGuard **shifts accessibility left** - all the way to your development workflow:

🔍 **During Development**
- Real-time scanning as you code
- AI-powered fix suggestions in your editor
- Interactive fixes before you even commit

🤖 **Learn from Your Team**
- Builds knowledge from past violations and fixes
- Learns patterns from git history automatically  
- Shares expertise across the team through incremental KB sync

🛡️ **Proactive Prevention**
- Pre-commit hooks block regressions
- Post-merge hooks auto-sync team knowledge
- AI predicts risky changes before they happen

💡 **Context-Aware Guidance**
- Multi-agent AI system with specialized expertise
- Explains WCAG standards in plain language
- Suggests fixes based on your codebase patterns

---

**💡 An Experiment in AI-Powered Accessibility**

**A11y Guard represents an experiment**: Can we extend GitHub Copilot SDK to create specialized AI agents that make accessibility expertise more accessible to every developer?

This tool demonstrates one possible future—where AI doesn't just help us write code faster, but helps us write better, more inclusive code by bringing domain expertise directly into our workflows.
That said, important caveats apply:

> 🤖 **AI is a tool, not an oracle**. The suggestions A11y Guard provides are AI-generated and can be wrong. Always review carefully, test thoroughly, and validate against WCAG guidelines and real-world usage with assistive technologies.

> 🛡️ **No replacement for human judgment**. This tool complements—but doesn't replace—manual testing, user research, and established accessibility tools. Think of it as an always-available accessibility consultant, not the final authority.

> ⚖️ **You are responsible**. This software is provided "as-is" without warranties. Users are responsible for their code's compliance with accessibility standards and regulations. The author accepts no liability for issues arising from the use of this tool.

The goal is not perfection—it's progress. By making accessibility testing faster and more integrated into daily workflows, we can all build a more inclusive web, one commit at a time.
---

## 🚀 Quick Start

### Prerequisites

Before you begin, ensure you have:

1. **GitHub Copilot CLI** installed and authenticated
   ```bash
   # Install Copilot CLI
   # Visit: https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli
   
   # Verify installation
   copilot --version
   ```

2. **Node.js 18+** installed
   ```bash
   node --version  # Should be 18.0.0 or higher
   ```

3. **A GitHub Copilot subscription**
   - GitHub Copilot Individual, Business, or Enterprise
   - The CLI uses your existing Copilot subscription
   - [Learn more about Copilot subscriptions](https://github.com/features/copilot)

### Installation

```bash
# Install globally via npm
npm install -g @democratize-quality/a11y-guard

# Or install in your project
npm install --save-dev @democratize-quality/a11y-guard
```

### Initialize in Your Project

```bash
# Initialize A11yGuard (creates DB, config, git hooks)
a11y-guard init

# You'll see:
# ✅ Database: ./.a11y-guard/knowledge-base.db
# ✅ Git hooks: pre-commit, pre-push, post-merge  
# ✅ Config: .a11yguard.config.json
```

### Your First Check

```bash
# Smart scan (default) - AI-predicted scanning
a11y-guard check

# Check with baseline comparison
a11y-guard check --compare-baseline

# Fast static analysis only
a11y-guard check --mode=static
```

---

## 🎯 Core Features

### 1. 🔍 Multi-Mode Scanning

Choose the right scanning mode for your workflow:

- **Smart Mode** (default): AI predicts which files need scanning
- **Static Mode**: Fast ESLint-based analysis  
- **Live Mode**: Real browser testing with accessibility tree inspection

[Learn more about scanning modes →](docs/commands/check.md)

### 2. 🤖 AI-Powered Fix Recommendations

Get intelligent, context-aware fixes powered by multi-agent AI:

```bash
# Interactive mode (default) - review and apply fixes one-by-one
a11y-guard fix src/components/Button.tsx

# Non-interactive - just show suggestions
a11y-guard fix src/components/Button.tsx --no-interactive
```

**Six Specialized AI Agents Work Together:**

- 🔎 **Scanner Agent**: Detects accessibility violations
- 📊 **History Agent**: Analyzes patterns from past fixes
- 🔧 **Fix Agent**: Generates contextual solutions
- 🎓 **Educator Agent**: Explains WCAG standards
- ⚠️ **Risk Agent**: Predicts regression likelihood
- 🐙 **GitHub Agent**: Integrates with repository operations

[Learn more about the fix command →](docs/commands/fix.md)

### 3. 🧠 Team Knowledge Base

A11yGuard learns from your team's accessibility fixes automatically:

```bash
# Learn from git history
a11y-guard learn

# Full re-learn (ignore last sync)
a11y-guard learn --full --commits 200

# Incremental learning (only new commits - default)
a11y-guard learn --incremental
```

**How Team Learning Works:**

1. Developer fixes accessibility issue → commits code
2. Teammate runs `git pull` → post-merge hook triggers
3. A11yGuard learns from diff automatically (silent)
4. Knowledge base syncs across the team
5. AI suggests better fixes based on team patterns

**No CI/CD infrastructure needed!** Just git + hooks.

[Learn more about the learn command →](docs/commands/learn.md)

### 4. 💬 Ask Anything About Accessibility

Get instant answers about accessibility patterns:

```bash
# Ask about specific issues
a11y-guard ask "What are common button accessibility issues?"

# Get WCAG guidance
a11y-guard ask "Explain ARIA roles for navigation"

# Learn from your codebase
a11y-guard ask "What accessibility patterns does our team use?"
```

The AI has full context of:
- Your knowledge base (past fixes and violations)
- WCAG 2.1 AA standards
- Your project structure and patterns

[Learn more about the ask command →](docs/commands/ask.md)

### 5. 📊 Historical Analysis

Understand accessibility trends in your codebase:

```bash
# Analyze patterns
a11y-guard analyze

# Analyze specific files
a11y-guard analyze src/components/*.tsx

# See what violations are most common
# See which fixes work best for your team
# Identify high-risk areas
```

[Learn more about the analyze command →](docs/commands/analyze.md)

---

## 🏗️ Architecture

A11yGuard uses a **multi-agent orchestration** architecture powered by the GitHub Copilot SDK:

```
┌─────────────────────────────────────────────┐
│         Orchestrator Agent                  │
│         (Coordinates all subagents)         │
└─────────────────┬───────────────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
┌──────────────┐    ┌──────────────┐
│   Scanner    │    │   History    │
│   Agent      │    │   Agent      │
│              │    │              │
│ • ESLint     │    │ • Pattern    │
│ • Live scan  │    │   detection  │
│ • MCP tools  │    │ • Stats      │
└──────────────┘    └──────────────┘
        │                   │
        ▼                   ▼
┌──────────────┐    ┌──────────────┐
│     Fix      │    │   Educator   │
│    Agent     │    │    Agent     │
│              │    │              │
│ • Solution   │    │ • WCAG       │
│   generation │    │   guidance   │
│ • Context    │    │ • Best       │
│   aware      │    │   practices  │
└──────────────┘    └──────────────┘
        │                   │
        ▼                   ▼
┌──────────────┐    ┌──────────────┐
│    Risk      │    │   GitHub     │
│   Agent      │    │   Agent      │
│              │    │              │
│ • Regression │    │ • Repo ops   │
│   prediction │    │ • PR context │
│ • Priority   │    │ • Diff       │
└──────────────┘    └──────────────┘
```

**Why Multi-Agent?**

- 🎯 **Specialization**: Each agent is expert in its domain
- 🔄 **Parallel Execution**: Multiple analyses run simultaneously
- 🧠 **Contextual Intelligence**: Agents share knowledge and context
- 🎨 **Flexible Orchestration**: Adapt workflow based on the task

---

## 📚 Command Reference

| Command | Description | Example |
|---------|-------------|---------|
| [`init`](docs/commands/init.md) | Initialize A11yGuard in your project | `a11y-guard init` |
| [`check`](docs/commands/check.md) | Scan for accessibility violations | `a11y-guard check --mode=smart` |
| [`fix`](docs/commands/fix.md) | Get AI-powered fix recommendations | `a11y-guard fix src/**/*.tsx` |
| [`analyze`](docs/commands/analyze.md) | Analyze historical patterns | `a11y-guard analyze` |
| [`ask`](docs/commands/ask.md) | Ask questions about accessibility | `a11y-guard ask "..."` |
| [`learn`](docs/commands/learn.md) | Build knowledge from git history | `a11y-guard learn` |

---

## 🔗 Git Integration

A11yGuard installs three git hooks automatically:

### 1. Pre-Commit Hook
**Blocks commits with accessibility regressions**

```bash
# Runs automatically on: git commit
# Command: a11y-guard check --compare-baseline --block-on-regression
# Behavior: Blocks commit if new violations found
# Bypass: git commit --no-verify
```

### 2. Pre-Push Hook
**Warns about issues before push**

```bash
# Runs automatically on: git push  
# Command: a11y-guard check --compare-baseline
# Behavior: Shows warnings but doesn't block
# Use: Catch issues before PR/code review
```

### 3. Post-Merge Hook (NEW)
**Auto-syncs knowledge base after pull**

```bash
# Runs automatically on: git pull (after merge)
# Command: a11y-guard learn --incremental --silent
# Behavior: Learns from new commits, syncs KB silently
# Result: Team knowledge stays synchronized
```

**Smart Hook Management:**
- Preserves existing hooks (backs them up)
- Appends A11yGuard content instead of overwriting  
- Won't create duplicates on re-initialization
- Cross-platform compatible (Windows, macOS, Linux)

---

## ⚙️ Configuration

A11yGuard creates `.a11yguard.config.json` in your project:

```json
{
  "scanMode": "smart",
  "blockOnRegression": true,
  "knowledgeBase": {
    "autoLearn": true,
    "commitLimit": 100
  },
  "exclude": [
    "**/node_modules/**",
    "**/dist/**",
    "**/*.test.{ts,tsx,js,jsx}"
  ]
}
```

---

## 💾 Data Storage

### Per-Repository Database

A11yGuard uses a **project-specific SQLite database**:

```
your-project/
  .a11y-guard/
    knowledge-base.db  ← All data stored here
  .a11yguard.config.json
  .gitignore  ← Automatically updated
```

**Why per-repository?**
- ✅ Each project has isolated knowledge
- ✅ Query results are project-specific  
- ✅ No global pollution or mixed data
- ✅ Easy to reset (just delete .a11y-guard/)

**What's stored:**
- Scan results and violations
- Successful fix patterns
- Commit history metadata
- Team learning progress (last learned commit)

**Git integration:**
- `.a11y-guard/` is automatically added to `.gitignore`
- Database is NOT committed to git
- Each developer has their own local DB
- KB syncs via incremental learning from git history

---

## 🎓 Documentation

### Command Guides
- [📘 init - Initialize A11yGuard](docs/commands/init.md)
- [📘 check - Scan for violations](docs/commands/check.md)  
- [📘 fix - AI-powered fixes](docs/commands/fix.md)
- [📘 analyze - Historical analysis](docs/commands/analyze.md)
- [📘 ask - Accessibility Q&A](docs/commands/ask.md)
- [📘 learn - Team knowledge building](docs/commands/learn.md)

### Onboarding Guide
- [🚀 Getting Started with Sample App](docs/ONBOARDING.md)

---

## 🤝 How A11yGuard Fits Your Workflow

### Development Workflow

```
1. Write code
   ↓
2. Run: a11y-guard fix <file>  ← Fix issues interactively
   ↓
3. git add && git commit       ← Pre-commit hook runs
   ↓
4. git push                    ← Pre-push hook warns
   ↓
5. Teammate: git pull          ← Post-merge hook auto-learns
```

### Team Collaboration

```
Developer A                     Developer B
    │                               │
    ├─ Fixes violation              │
    ├─ Commits fix                  │
    ├─ Pushes to remote             │
    │                               │
    │                           ├─ git pull
    │                           ├─ post-merge hook runs
    │                           ├─ learns from Developer A's fix
    │                           └─ KB updated automatically
```

---

## 🔧 Troubleshooting

### Copilot CLI Not Found

```bash
# Verify Copilot CLI is installed
copilot --version

# If not found, install from:
# https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli

# Verify authentication
copilot --status
```

### Git Hooks Not Running

```bash
# Check if hooks are executable
ls -la .git/hooks/pre-commit

# Should show: -rwxr-xr-x (executable)

# Re-initialize if needed
a11y-guard init
```

### Database Issues

```bash
# Reset database (WARNING: Deletes all learned patterns)
rm -rf .a11y-guard/
a11y-guard init

# Or just re-learn from git
a11y-guard learn --full --commits 200
```

---

## 📊 Token Usage & Costs

A11yGuard uses GitHub Copilot's API, which is **included in your Copilot subscription**:

- **Copilot Individual/Business/Enterprise**: No additional API costs
- **Token tracking**: Displayed after each command  
- **Caching**: Prompt caching reduces token usage significantly

Example token usage:
```
✅ Fix applied successfully!

📊 Token Usage:
  Input tokens:    62,288 (46,179 cached)
  Output tokens:    1,298
  Cache tokens:    46,179
  Total tokens:    63,586
```

---

## 🌟 Why A11yGuard?

| Feature | Lighthouse | axe-core | A11yGuard |
|---------|-----------|----------|-----------|
| **When it runs** | After build | After build | **During development** |
| **AI-powered fixes** | ❌ | ❌ | **✅** |
| **Learns from team** | ❌ | ❌ | **✅** |
| **Pre-commit blocking** | ❌ | ❌ | **✅** |
| **Interactive fixes** | ❌ | ❌ | **✅** |
| **Context-aware** | ❌ | ❌ | **✅** |
| **Git integration** | ❌ | ❌ | **✅** |
| **Multi-agent AI** | ❌ | ❌ | **✅** |

**Use A11yGuard alongside** Lighthouse and axe-core:
- A11yGuard: Development-time prevention
- Lighthouse/axe-core: Pre-production validation

---

## 📜 License

MIT

---

## 🙏 Acknowledgments

Built with:
- [GitHub Copilot SDK](https://github.com/github/copilot-sdk) - AI-powered agent framework
- [ESLint jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y) - Static accessibility rules
- [Axe-core](https://github.com/dequelabs/axe-core) - Accessibility testing engine

---

## 🚀 Get Started Now

```bash
# 1. Install
npm install -g a11y-guard

# 2. Initialize
a11y-guard init

# 3. Check your code
a11y-guard check

# 4. Fix issues interactively
a11y-guard fix src/components/Button.tsx

# 5. Ask questions
a11y-guard ask "What are common form accessibility issues?"
```

**Make accessibility a first-class citizen in your development workflow** - not an afterthought. 🛡️

---

*Built with ❤️ for inclusive web development quality by Democratize-Quality*
