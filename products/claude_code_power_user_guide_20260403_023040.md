# Claude Code Power User Guide

> Comprehensive tutorial covering Claude Code slash commands, custom CLAUDE.md setup, multi-agent workflows, and real-world project examples. Targets developers new to agentic coding.

---

**Version:** 1.0
**Published:** April 2026
**License:** Personal use only. Redistribution prohibited.

---

## Table of Contents

1. Getting Started with Claude Code
2. CLAUDE.md -- Your Project Constitution
3. Slash Commands and Power Features
4. Multi-Agent Workflows
5. Real-World Workflow Examples

---

## Chapter 1: Getting Started with Claude Code

Claude Code is an agentic coding assistant that operates directly in your terminal.
Unlike chat-based tools, it reads your files, runs commands, and makes changes
autonomously -- all under your supervision.

### Installation

```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

### First Run

Navigate to any project directory and type:

```bash
claude
```

Claude will automatically read your CLAUDE.md (if present), explore the project
structure, and be ready for your first instruction.

### What Claude Can Do Out of the Box

- Read and write any file in your project
- Run shell commands (build, test, lint, git)
- Search the web via WebFetch
- Spawn subagents via the Task tool for parallel work
- Remember context across sessions via /memory

### What It Cannot Do (Without Setup)

- Modify files outside the working directory (sandbox protection)
- Access services that require API keys you have not configured
- Push to git remotes without your confirmation

## Chapter 2: CLAUDE.md -- Your Project Constitution

CLAUDE.md is the most important file for Claude Code. It is loaded automatically
at session start and defines how Claude should behave in your project.

### Minimal Effective CLAUDE.md

```markdown
# Project: MyApp

## Stack
- Python 3.12, FastAPI 0.115, SQLAlchemy 2.0
- Tests: pytest + pytest-asyncio
- Linter: ruff (check + format)
- Package manager: uv (never pip directly)

## Key Commands
- Build: `uv sync`
- Test: `uv run pytest -x -q`
- Lint: `uv run ruff check . && uv run ruff format --check .`

## Rules
- Never commit to main directly
- All new code must have type annotations
- Max 400 lines per file
```

### What to Include

1. **Tech stack** -- so Claude picks the right imports and patterns
2. **Common commands** -- so Claude can build/test without guessing
3. **Coding conventions** -- naming, file size limits, patterns you prefer
4. **Forbidden actions** -- files that must not be modified, branches to protect

### Global vs Project CLAUDE.md

You can also create `~/.claude/CLAUDE.md` for rules that apply to all projects:
style preferences, tool preferences, personas for subagents.

## Chapter 3: Slash Commands and Power Features

### Essential Commands

| Command | Effect |
|---------|--------|
| `/clear` | Wipe conversation history (keeps CLAUDE.md) |
| `/compact` | Summarize history to free up context |
| `/memory` | View and edit persistent memory |
| `/cost` | Show token usage and estimated spend |
| `/model [name]` | Switch model (sonnet/opus/haiku) |

### Effective Conversation Patterns

**Specify acceptance criteria upfront:**
```
Add user authentication. Requirements:
- JWT tokens, 24h expiry
- POST /auth/login returns {token, user}
- 401 on bad credentials, 429 after 5 failures
- Tests for all three cases
```

**Ask for proof, not promises:**
```
Prove this works -- show me the test output.
```

**Challenge the solution:**
```
What could go wrong with this implementation?
List edge cases you have not tested.
```

### Multi-File Refactors

For large changes, give Claude a plan phase first:
```
Plan (do not write code yet): how would you restructure
the auth module to support OAuth in addition to JWT?
List every file that would need to change.
```
Review the plan, then:
```
Now implement it. Start with the tests.
```

## Chapter 4: Multi-Agent Workflows

Claude Code can spawn subagents via the Task tool, enabling parallel workloads
that would otherwise fill the context window.

### When to Use Subagents

- Analysing multiple large files simultaneously
- Running independent research tasks in parallel
- Isolating risky operations (subagent can fail safely)
- Context-heavy operations (each subagent has its own window)

### Subagent Sandbox Modes

```
sandbox=read-only       -- can read files, cannot write
sandbox=workspace-write -- can read and write project files
```

**Rule:** Use read-only for analysis, workspace-write for code generation.

### Practical Pattern: Parallel Review

```
Spawn three subagents in parallel:
- Agent 1 (read-only): Review src/auth/ for security issues
- Agent 2 (read-only): Review src/api/ for performance issues
- Agent 3 (read-only): Review tests/ for missing coverage
Summarize findings when all three complete.
```

### Passing Context Efficiently

Pass file paths rather than inline content:
```
# Efficient: subagent reads the file itself
"Analyse /path/to/large_module.py and report issues"

# Wasteful: fills orchestrator context
"Here is the content: [10000 lines pasted]"
```

## Chapter 5: Real-World Workflow Examples

### Bug Fix Workflow

```
1. "The /api/users endpoint returns 500 on empty query params.
   Find the bug and fix it."
2. Claude: reads endpoint, finds issue, writes fix + test
3. "Run the tests and show me the output."
4. Claude: runs pytest, reports results
5. "Commit this as fix: handle empty query params in users endpoint"
```

### Feature Development Workflow

```
1. "Add CSV export to the reports module.
   Use Python's csv module. Support: all columns, date range filter,
   UTF-8 BOM for Excel compatibility."
2. "Write the tests first."
3. Claude: writes tests (all failing)
4. "Now implement until all tests pass."
5. Claude: implements, runs tests, reports green
6. "Create a PR against main."
```

### Code Review Workflow

```
1. "Review this PR diff for: security issues, missing error handling,
   and test coverage gaps. Be specific about line numbers."
2. Claude: reads diff, produces structured review
3. "Fix the two security issues you found."
4. Claude: applies fixes
5. "Show me the diff of your changes."
```

### Refactoring Workflow

```
1. "Identify all places where we are catching bare Exception.
   List file:line for each."
2. Review the list
3. "Replace each with specific exception types.
   Ensure tests still pass after each change."
```

---

*Thank you for purchasing this guide. If you found it useful, reviews and
word-of-mouth help more than anything else.*
