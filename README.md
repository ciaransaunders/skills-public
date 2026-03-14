# Claude Skills — Public

A collection of reusable skill definitions for Claude (Cowork / Claude Code). Each skill is a set of instructions that shapes how Claude approaches a specific type of task.

## Skills

### [`draco-research-promptgen`](./draco-research-promptgen/)
Transform vague research requests into optimised deep research prompts. Based on the DRACO benchmark methodology, applying three quality pillars (Objectivity, Boundedness, Challenge) and six augmentation dimensions (Persona, Output, Source, Temporal, Cross-entity, Geography).

### [`employment-law-verifier`](./employment-law-verifier/)
Expert verification of UK employment law documents to tribunal and appellate standards. Covers ET/EAT skeleton arguments, appeal grounds, witness statements, Schedule of Loss, and legal correspondence. Classifies problems as Critical Errors or Justification Gaps.

### [`legal-writing-quality`](./legal-writing-quality/)
Review, draft, and improve legal writing — correspondence, case analysis reports, memos, and skeleton arguments — applying SQE-level standards and the IRAC methodology. Built on *Written Skills for Lawyers* (5th ed.).

### [`prompt-architect`](./prompt-architect/)
Create and optimise prompt templates for AI assistants and autonomous agents. Covers chat mode vs. autonomous mode design, model-specific patterns for Claude 4.6, GPT-5.4, and Gemini 3, and a full library of structured blocks for agentic workflows.

## How to Use

Each skill folder contains a `SKILL.md` file with the full instruction set. Some skills include a `references/` folder with supporting material (domain patterns, examples, checklists).

To use a skill with Claude Cowork or Claude Code, install it as a skill in your session or paste the contents of `SKILL.md` into your system prompt.

## Licence

MIT — use freely, adapt as needed.
