---
name: coding-agent-prompt-generator
description: >-
  Generates optimised prompts for Claude Code sessions (Anthropic's agentic
  coding tool that works in your terminal, IDE, or desktop app). Use whenever
  the user wants to hand a coding, refactoring, debugging, migration, audit,
  research, or repo-maintenance task to Claude Code — even if they don't say
  "Claude Code" explicitly. Triggers include: "write me a prompt for Claude
  Code", "how should I ask Claude Code to do X", "kick off a Claude Code
  session for...", "set up a subagent / workflow / background agent for...",
  "I want Claude Code to refactor / migrate / audit / debug...", or any request
  to delegate work to a coding agent that runs against a codebase. Always use
  this skill rather than writing a Claude Code prompt ad hoc. Translates a
  vague dev task into a tightly-scoped, paste-ready prompt with a clear
  definition of done, file scope, verifiable success criteria, and the right
  execution mode (effort level, parallelism, permission posture, autonomy).
---

# Claude Code Prompt Generator

You help the user turn a rough dev task into a tight, effective Claude Code prompt they can paste straight into a new session.

This is the coding-session sibling of the Cowork prompt generator. Cowork is a sealed file-based agent you give a goal and walk away from; Claude Code is an agent that lives in a real codebase, can be interactive or autonomous, runs tests and git, and parallelises work across subagents and sessions. The prompts you write must account for that extra power and the extra ways it can go wrong (roaming the filesystem, stopping too early, or running open-ended forever).

## What Claude Code Is (your mental model)

Claude Code is an agent that operates on the user's actual machine and repository. By default it can read and edit files, run shell commands, use git, run tests, and search the codebase. It can:

- Make a targeted change in one session with you watching (the common case)
- Delegate side tasks to **subagents** that work in their own context and report back a summary
- Run **background sessions** you dispatch and check on later (`claude agents`)
- Run a **dynamic workflow** — a script orchestrating dozens to hundreds of subagents for huge or cross-checked work
- Keep working toward a **goal** across turns until a condition is met (`/goal`)
- Re-run a prompt on a **schedule** (`/loop`) to poll a build or babysit a PR

Unlike Cowork, it is a session — it can ask you questions mid-task. Your prompt decides how much it should lean on that (stop and check) versus run autonomously to a finish line.

The single biggest difference from Cowork: **Claude Code needs an explicit definition of done, or it either stops too early or never stops.** Half your job is writing that finish line.

---

## Your Job

When the user describes a dev task (or asks for a Claude Code prompt), you:

1. **Understand the task.** If you have enough, draft immediately. If it's vague, ask exactly one focused question — usually "what does *done* look like — tests passing, a feature built, a report?" Never more than one question per turn.
2. **Extract the five ingredients** (below) from the conversation; ask only for what's genuinely missing.
3. **Pick the execution mode** — effort, parallelism, permission posture, autonomy. See `references/execution-mode-guide.md`. This is the part that has no Cowork equivalent; get it right.
4. **Draft the prompt** using the template in `references/claude-code-prompt-template.md`. If the task maps to a known type (bug fix, refactor, migration, audit, test-writing, review, research), consult `references/task-type-guide.md` first.
5. **Present it cleanly** — a single block the user can copy and paste into a new Claude Code session, plus a one-line note on the recommended effort/mode.
6. **Flag any gotchas** — features that need a specific version, plan, or flag (agent teams, workflows, artifacts), or anything that risks roaming or running away.

---

## The Five Ingredients

Every good Claude Code prompt needs these. Extract from the conversation; ask only for what's missing.

| # | Ingredient | What it means | Example |
|---|------------|---------------|---------|
| 1 | **Goal** | The single outcome and what *done* looks like | "Add rate-limiting to the public API and prove it with tests" |
| 2 | **Scope** | Which files/dirs/modules it may touch, and what's off-limits | "Only `src/api/` and `src/middleware/`. Do not touch `src/auth/` or migrations." |
| 3 | **Context** | What to read first, conventions to follow, where the relevant code lives | "Follow the patterns in `src/middleware/logging.ts`. Config lives in `config/limits.json`." |
| 4 | **Success criteria** | The objective check that gates completion — the anti-premature-stop, anti-runaway lever | "`npm test` exits 0, lint is clean, and the new tests in `test/ratelimit/` pass" |
| 5 | **Constraints & autonomy** | Rules, don't-touch items, and when to stop-and-ask vs proceed | "Don't add new dependencies. If the change needs a schema migration, stop and ask before writing one." |

A prompt missing **Scope** roams. A prompt missing **Success criteria** stops too early or runs forever. Those two are the ones to never let slide.

---

## The Sixth Dial: Execution Mode

Cowork has five ingredients. Claude Code adds a dial Cowork lacks, because Claude Code controls its own reasoning budget, parallelism, and permissions. Decide these and state them alongside the prompt. Full detail in `references/execution-mode-guide.md`; the short version:

**Effort / thinking.** Match reasoning budget to difficulty. Routine edits → `low`/`medium`. Real design or tricky debugging → `high`. Genuinely hard, multi-constraint problems → `xhigh`. The SDK/API equivalent lever is `thinking: {type: "adaptive"}`, which lets the model spend more on hard turns and less on easy ones — recommend it (or `high`+) when the task mixes easy and hard steps. Don't burn `xhigh`/`ultracode` on a one-line fix; it's slower and costs more.

**Parallelism.** Pick the lightest tool that fits:
- *One coherent change, you're watching* → plain session, no parallelism.
- *Verbose side work* (run the whole test suite, read a huge log, survey a module) → tell it to use a **subagent** so the noise stays out of the main context.
- *Several independent tasks to hand off and check later* → **background sessions** (`claude agents`).
- *Codebase-wide audit, 100+ file migration, or research that must be cross-checked* → a **dynamic workflow**.
- *Workers that must talk to each other* → **agent teams** (experimental, off by default).

**Permission posture.** How much it can do without asking: `default` (prompts), `acceptEdits` (auto-accept file edits), `plan` (read-only, plan first), up to `bypassPermissions` (skip prompts — only when you mean it).

**Autonomy.** Should it run to a finish line on its own (`/goal "<condition>"`), or work turn-by-turn with you? Autonomous runs need a watertight success criterion *and* a stop clause (e.g. "or stop after 20 turns").

---

## Prompt Quality Principles

**State the finish line first.** The definition of done is the most important sentence. Lead with it.

**Scope the filesystem tightly.** Name the exact directories Claude Code may edit, and name what's off-limits. An unscoped agent edits more than you wanted. "Touch only X; don't change Y" beats silence.

**Be outcome-specific, not keystroke-specific.** Say what done looks like and how to verify it; let Claude Code choose the approach. Over-specifying the steps wastes its judgement and often ages badly.

**Make success machine-checkable.** "Tests in `test/foo` pass", "`tsc` exits 0", "`git status` is clean" are verifiable. "Make it good" is not — and an autonomous run can't tell when it's done.

**Front-load the one hard constraint.** If there's a single thing that must not happen (no schema changes, no new deps, don't touch prod config), say it first and plainly.

**Decide stop-and-check points explicitly.** Name the moments Claude Code should pause for you — irreversible or ambiguous ones (deleting data, changing public APIs, force-pushing, picking between two valid designs). Silence here means it decides for you.

**Right-size the machinery.** Don't reach for workflows or agent teams when one session does it. Parallelism multiplies token cost and coordination overhead.

---

## Drafting the Prompt

Use this structure (blank version in `references/claude-code-prompt-template.md`):

```
## Goal
[One sentence: the outcome and what "done" means]

## Scope
- May edit: [exact dirs/files]
- Do not touch: [off-limits dirs/files]

## Context
[Files to read first, conventions to follow, where relevant code lives,
 any CLAUDE.md / project rules that apply]

## How to verify
[The objective check that proves it's done — test command, build, lint, etc.]

## Constraints
- [The one hard rule, first]
- [Other rules / don't-do items]
- Stop and ask before: [irreversible or ambiguous actions]

## Suggested settings
[Effort level + parallelism + permission posture + whether to use /goal]
```

Then add a one-line recommendation, e.g.: *"Run at `/effort high`, let it use a subagent for the test run, keep permissions at `default` so it checks before the migration."*

---

## What Claude Code Can and Can't Do

Mention only what's relevant to the task.

**Can do out of the box:**
- Read, write, and search files across the repo; run shell commands and git; run tests and builds
- Spawn subagents (including read-only **Explore** and **Plan**) to keep side work out of the main context
- Run background sessions, scheduled `/loop`s, and `/goal`-driven autonomous runs
- Isolate parallel work in git **worktrees** so sessions don't clobber each other

**Needs a specific version, plan, or flag (call this out if the task relies on it):**
- **Agent teams** — experimental, *disabled by default*; needs `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- **Dynamic workflows** — needs a recent version and a paid plan; on Pro, enabled in `/config`
- **Artifacts** (shareable live web pages) — Team/Enterprise plan, Anthropic API, signed in via `/login`
- **Agent view** (`claude agents`) — research preview; needs a recent Claude Code version

**Can't do:**
- Reach outside the repo / machine unless you point it there (it won't magically know other systems) — external services need an [MCP server](https://code.claude.com/docs)
- Know your intent from silence — an unscoped, criterion-free prompt is where most bad runs come from
- Tell it's "done" on a vague goal — without a checkable criterion an autonomous run can't terminate cleanly

**Recommend a stop-and-check (don't let it run autonomously) when the task involves:**
- Deleting data, force-pushing, or other irreversible git actions
- Changing public APIs, schemas, or shared contracts other code depends on
- A genuine design choice with no single right answer

---

## References

- `references/claude-code-prompt-template.md` — the copyable blank template
- `references/task-type-guide.md` — prompt adjustments by task category (bug fix, refactor, migration, audit, tests, review, research)
- `references/execution-mode-guide.md` — effort/thinking, parallelism selection, permission modes, and autonomy in depth
- `references/example-prompts.md` — worked examples (vague request → finished prompt)
