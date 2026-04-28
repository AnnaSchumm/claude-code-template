# How to Use This Template

## The core idea

Claude has no memory between sessions. Every time you open a new chat, it starts fresh. This template solves that by giving Claude a structured folder of files to read at the start of every session — so it always knows how to behave, what your project is, and where work left off.

---

## The folders — what, when, and why

### `CLAUDE.md` (root)
**What:** The first thing Claude reads every session. Your project's entry point.
**When:** Edit this when you start a new project. Put your project description, tech stack, and point Claude to the right folders for each task type.
**Why:** Without this, Claude treats every session as if it's seeing your project for the first time.

---

### `.claude/rules/`
**What:** Permanent behavioral instructions. Includes `code-style.md`, `brand-voice.md`, `api-conventions.md`.
**When:** Add a rule when you've corrected Claude more than once on the same thing — e.g. "stop using passive voice" or "always use snake_case". Write it once here, never repeat yourself again.
**Why:** Rules apply to every task automatically. Claude checks these before producing any output.

---

### `.claude/context/`
**What:** Things Claude should *know* about your project — your audience, approved sources, topics.
**When:** Fill these in when Claude keeps getting your domain wrong. If you're building a product for doctors and Claude writes like it's for developers, put that in `audience.md`.
**Why:** Separates "what Claude knows" from "how Claude behaves" — knowledge shouldn't be mixed into rules.

---

### `.claude/commands/`
**What:** Slash commands you trigger manually. Includes `/project:review`, `/project:deploy`, `/project:fix-issue`.
**When:** Type `/project:review` in Claude Code when you want a structured code review of your staged changes. Claude follows the exact process defined in the command file — reads changed files, checks against your rules, outputs a categorized report.
**Why:** Gives you repeatable, consistent workflows instead of re-explaining what you want each time.

---

### `.claude/agents/`
**What:** Subagent personas — specialized Claude instances with defined roles and limits. Includes `code-reviewer`, `research-agent`, `security-auditor`.
**When:** You don't trigger these directly. Commands and skills invoke them automatically.
**Why:** Agents have defined scope (e.g. "can read files, cannot modify them") which prevents Claude from doing more than you intended during a workflow.

---

### `.claude/skills/`
**What:** Auto-triggered workflows. Includes `security-review` and `deploy`. The `index.md` maps trigger conditions to skills.
**When:** You don't trigger these — they fire automatically. For example, if you modify any file in `/auth`, the `security-review` skill triggers without you asking.
**Why:** For things that should *always* happen in certain situations, removing the human step of remembering to ask.

---

### `.claude/schemas/`
**What:** JSON schemas defining the exact shape of Claude's outputs — review reports, security reports, task handoffs.
**When:** Referenced by agents automatically. Add a new schema when you add an agent that produces structured data.
**Why:** Ensures Claude's output can be reliably parsed or fed into another tool or agent. Prevents Claude from improvising the format.

---

### `.claude/protocols/`
**What:** Contracts for how agents hand off work to each other — required fields, format, fallback behavior.
**When:** Only relevant when you have multi-agent workflows where one agent's output becomes another's input.
**Why:** Without a protocol, agent A's output might not match what agent B expects.

---

### `.claude/memory/`
**What:** Runtime state — `state.json` tracks what workflow is in progress, what's done, what's pending. Gitignored.
**When:** Claude writes to this automatically during workflows. You can read it to see where a workflow left off.
**Why:** Lets multi-step workflows survive across sessions. If a deploy workflow is interrupted, Claude can resume where it stopped.

---

## How it all connects — a practical example

You type `/project:review` in Claude Code:

1. Claude reads `CLAUDE.md` → knows the project
2. Loads `.claude/commands/review.md` → knows the process
3. Spawns the `code-reviewer` agent from `.claude/agents/code-reviewer.md` → scoped to read-only
4. Agent checks your staged changes against `.claude/rules/code-style.md` → applies your standards
5. Outputs a report shaped by `.claude/schemas/review-report.json` → always the same structure
6. Updates `.claude/memory/state.json` → records what was done

---

## When does this setup pay off?

| Situation | What helps most |
|-----------|----------------|
| Solo project | `rules/` and `context/` — stops you repeating yourself |
| Team project | Rules become shared standards; everyone's Claude behaves the same way |
| Complex workflows | Commands + agents + skills — automates multi-step processes |
| Long-running project | Memory keeps Claude oriented across sessions |

---

## Getting started checklist

1. Copy this template into your project
2. Edit `CLAUDE.md` with your project description
3. Fill in `.claude/rules/code-style.md` with your coding standards
4. Fill in `.claude/context/audience.md` with who you're building for
5. Try `/project:review` on your first PR
6. Add rules whenever you catch yourself correcting Claude twice on the same thing
