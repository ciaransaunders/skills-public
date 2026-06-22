# Claude Skills — Public

A collection of reusable skill definitions for Claude (Cowork / Claude Code). Each skill is a set of instructions that shapes how Claude approaches a specific type of task.

## Skills

### [`bailii-case-download`](./bailii-case-download/)
Search BAILII (www.bailii.org) for a UK or Irish case and download the full judgment as a clean PDF. Handles BAILII's Anubis bot-wall by solving the proof-of-work challenge in pure Python — no browser required. Supports lookup by neutral citation (e.g. `[2024] EAT 114`), by direct BAILII URL, or by case name search. Strips BAILII chrome, navigation, donation banners, and footers. Useful for building bundles of authorities.

### [`coding-agent-prompt-generator`](./coding-agent-prompt-generator/)
Generates optimised prompts for Claude Code sessions (Anthropic's agentic coding tool). Use to delegate coding, refactoring, debugging, migration, audit, or repo-maintenance tasks to Claude Code. Translates a vague dev task into a tightly-scoped, paste-ready prompt with a clear definition of done, file scope, verifiable success criteria, and the right execution mode (effort level, parallelism, permission posture, autonomy).

### [`cowork-prompt-generator`](./cowork-prompt-generator/)
Generates optimised prompts for Claude Cowork (Anthropic's autonomous file-based agent mode). Use to delegate file-based, document, or multi-step tasks to Cowork — organising files, synthesising documents, bulk processing, or producing professional output from local files. Translates a vague goal into a structured, workspace-scoped prompt with clear outcomes, file scope, constraints, and output format.

### [`draco-research-promptgen`](./draco-research-promptgen/)
Transform vague research requests into optimised deep research prompts. Based on the DRACO benchmark methodology, applying three quality pillars (Objectivity, Boundedness, Challenge) and six augmentation dimensions (Persona, Output, Source, Temporal, Cross-entity, Geography).

### [`employment-law-verifier`](./employment-law-verifier/)
Expert verification of UK employment law documents to tribunal and appellate standards. Covers ET/EAT skeleton arguments, appeal grounds, witness statements, Schedule of Loss, and legal correspondence. Classifies problems as Critical Errors or Justification Gaps.

### [`legal-writing-quality`](./legal-writing-quality/)
Review, draft, and improve legal writing — correspondence, case analysis reports, memos, and skeleton arguments — applying SQE-level standards and the IRAC methodology. Built on *Written Skills for Lawyers* (5th ed.).

### [`prompt-architect`](./prompt-architect/)
Create and optimise prompt templates for AI assistants and autonomous agents. Covers chat mode vs. autonomous mode design, model-specific patterns for Claude Fable 5 / Mythos 5, Claude Opus 4.8, GPT-5.5, and Gemini 3.5 Flash, and a full library of structured blocks for agentic workflows (verification loops, completeness contracts, tool persistence, memory systems, scope boundaries, and more).

### [`xquik-public-x-research`](./xquik-public-x-research/)
Collect and validate public X evidence with Xquik's REST API. Covers search, timelines, threads, trends, monitors, exports, and provenance guardrails for public or user-authorized X data.

## How to Use

Each skill folder contains a `SKILL.md` file with the full instruction set. Some skills include a `references/` or `scripts/` folder with supporting material (domain patterns, examples, checklists, helper scripts).

To use a skill with Claude Cowork or Claude Code, install it as a skill in your session or paste the contents of `SKILL.md` into your system prompt.

## Licence

MIT — use freely, adapt as needed.
