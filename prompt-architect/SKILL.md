---
name: prompt-architect
description: Create and optimize reusable prompt templates for AI assistants and autonomous agents. Use when user says "make a prompt for this task", "improve this prompt", "write a prompt template", "create a prompt for [task]", "optimize this prompt", or requests help designing instructions for LLMs or agents. Also trigger when the user asks about prompting best practices, how to prompt a specific model (Claude Fable 5, Claude Opus 4.8, GPT-5.5, Gemini 3.x), or wants to translate a prompt from one model to another. Incorporates vendor-specific prompt engineering guidance from Anthropic (Claude Fable 5/Mythos 5, Claude Opus 4.8), OpenAI (GPT-5.5), and Google (Gemini 3.5 Flash), plus research on the Prompting Inversion, Over-Specification Paradox, and the four-layer instruction stack for autonomous agents.
---

# Prompt Architect

Create reusable prompt templates optimised for modern LLMs and autonomous agents. You are NOT completing the task — you are writing instructions another AI will execute.

## Step 0: Determine Execution Mode and Target Model

Before anything else, determine two things. Ask the user OR infer from context:

### A. Execution Mode

| Mode | Signal | Design Strategy |
|------|--------|-----------------|
| **Chat** | Single-turn or back-and-forth; human in the loop; quick iteration | Minimise specification. Over-specification penalty applies. |
| **Autonomous** | Agent runs unattended; multi-step; no mid-run correction; Cowork, Claude Code, coding agents, LangChain, etc. | Maximise specification. Completeness is the bottleneck. Under-specification causes silent failure. |

### B. Target Model

| Model Family | Key Prompt Characteristics |
|-------------|---------------------------|
| **Claude Fable 5 / Mythos 5** | Effort `high` default; `xhigh` for capability-sensitive workloads; `medium`/`low` for routine/interactive; **adaptive thinking only — no extended thinking budgets; summarized-only thinking output**; individual turns can run for many minutes — adjust client timeouts and streaming before deploying; **one brief instruction steers an entire behaviour pattern** (no need to enumerate); can over-elaborate at high/xhigh — use a brevity instruction; parallel subagents dispatched more readily than prior models; safety classifiers cover offensive cybersecurity + biology → `stop_reason: "refusal"` — configure fallback to Opus 4.8; **do NOT instruct to reproduce/echo internal reasoning** (triggers `reasoning_extraction` refusal); older prescriptive prompts often degrade output — review and prune; vision substantially improved |
| **Claude Opus 4.8** (and Opus 4.7/4.6, Sonnet 4.6, Haiku 4.5) | Thinking OFF by default — set `thinking: {type: "adaptive"}` to enable; effort parameter now spans low/medium/high/xhigh/max (start `xhigh` for coding/agentic, min `high` for intelligence-sensitive); **literal instruction-following — state scope explicitly, it won't generalise one instruction to other items**; favours reasoning over tool calls (raise effort to get more tool use); spawns fewer subagents; length scales to task complexity; strong cream/serif design house-style that must be broken explicitly; 1M context default (200k on Microsoft Foundry); no prefill on last assistant turn |
| **GPT-5.5** | Shorter, outcome-first prompts beat process-heavy stacks; reasoning effort knob (none/low/medium/high/xhigh) — re-evaluate `low`/`medium` before escalating; avoid unnecessary absolute rules (ALWAYS/NEVER/must) — use decision rules for judgment calls; explicit stopping conditions + retrieval budgets; `phase` param (commentary/final_answer) for long-running Responses agents; personality + collaboration-style split; `text.verbosity` control |
| **Gemini 3.5 Flash** (and 3.1 Pro, 3.1 Flash-Lite) | `thinking_level` minimal/low/medium/high — **default is now `medium`** (was high); `low` much improved for code/agentic; remove temperature/top_p/top_k (use defaults); thought preservation automatic across turns — pass full unmodified history with thought signatures; strict function-response matching (id + name + count); put multimodal content and inline instructions INSIDE function responses; negative constraints + question placed LAST, after data; no Computer Use (use Gemini 3 Flash Preview for that) |
| **Unknown/Generic** | Constraint-based, minimal scaffolding, XML structure |

**This distinction governs everything below.** Chat-mode prompts and autonomous-agent specifications are different design problems with opposite failure modes. Model-specific patterns prevent common pitfalls.

---

## Chat Mode

### Core Principle: Specification Threshold

Quality follows a non-monotonic curve. Below threshold S* ≈ 0.5, detail improves performance. Beyond it, additional specification causes **quadratic degradation** ("cognitive leakage"). Aim for maximum clarity with minimum structural overhead.

### Model-Specific Chat Guidance

#### Claude Opus 4.8

- **Thinking is OFF by default.** Unlike 4.6, Opus 4.8 does not think unless you set `thinking: {type: "adaptive"}`. Enable it for multi-step reasoning, agentic loops, and complex coding. If it thinks more often than you want (common with large system prompts), steer it: "Thinking adds latency and should only be used when it will meaningfully improve answer quality — when in doubt, respond directly."
- **Effort now spans low/medium/high/xhigh/max.** This is the primary depth lever — more important than on any prior Opus. Start `xhigh` for coding/agentic work, minimum `high` for intelligence-sensitive tasks, `medium`/`low` for latency- or cost-sensitive scoped work. If reasoning looks shallow, raise effort rather than prompting around it. `max` can overthink — test before adopting.
- **It follows instructions literally and does NOT generalise.** This is the biggest behavioural shift. If an instruction should apply broadly, say so: "Apply this to every section, not just the first." Do not assume it will infer scope from one example. The upside is precision for structured extraction and pipelines.
- **It favours reasoning over tool calls.** If you want more tool use (search, file reads), raise effort to `high`/`xhigh` and/or describe explicitly when and why to use each tool.
- **Length scales to task complexity** rather than a fixed verbosity. If you need a consistent style, specify it; positive examples of the right concision beat "don't be verbose."
- **Fewer subagents and tighter user updates by default.** Remove old scaffolding that forced interim status messages ("summarise every 3 tool calls") — it now produces good updates on its own.
- **Design work has a strong, persistent house style** (cream `#F4F1EA`, serif display, terracotta accent). Generic "don't use cream" just swaps to another fixed palette. To break it, either specify a concrete alternative palette/typeface, or ask it to propose 3-4 directions first and pick one.
- **No prefilled responses** on the last assistant turn. Use structured outputs, XML tags, or direct instructions.
- **Prefer telling Claude what TO do**, and **XML tags** for structuring inputs/outputs/instructions.

#### GPT-5.5

- **Outcome-first, not process-heavy.** GPT-5.5's biggest shift: describe the destination — target outcome, success criteria, constraints, available evidence, and what the final answer must contain — then let the model choose the path. Do NOT carry over every step from an older GPT-5.x prompt stack; legacy process-spelling adds noise and narrows its search.
- **Avoid unnecessary absolute rules.** Reserve ALWAYS/NEVER/must/only for true invariants (safety, required output fields, actions that must never happen). For judgment calls — when to search, ask, use a tool, keep iterating — write decision rules instead.
- **Add explicit stopping conditions.** State when to stop, retry, fall back, or ask. Example: "Resolve the request in the fewest useful tool loops, but do not let loop-minimisation outrank correctness or required citations. After each result ask: can I answer the core request now? If yes, answer."
- **Reasoning effort: re-evaluate low/medium before escalating.** GPT-5.5 reasons more efficiently than 5.4, so lower levels often suffice. It remains a last-mile knob — try a stronger prompt first.
- **Output contracts and follow-through policy still work** (named XML blocks like `<output_contract>`, `<default_follow_through_policy>`), but keep them lean. Set `text.verbosity` (default medium; `low` for concise).
- **Personality + collaboration style, kept short and separate.** Personality controls how it sounds (tone, warmth, directness); collaboration style controls how it works (when it asks vs assumes, how proactive, when it checks work). Neither replaces clear goals or stop rules.
- **Preamble for perceived latency.** For tool-heavy/multi-step tasks, ask for a one-to-two-sentence visible update acknowledging the request and stating the first step before tool calls.
- **`phase` for long-running Responses agents.** Use `phase: "commentary"` for interim updates and `phase: "final_answer"` for the answer; if manually replaying assistant items, preserve each `phase` value unchanged.

#### Gemini 3.5 Flash (and Gemini 3.x)

- **Remove temperature, top_p, and top_k.** All Gemini 3.x reasoning is optimised for default sampling. Changing them causes looping and degraded reasoning. For determinism, write explicit rules in a system instruction instead.
- **`thinking_level` controls depth — default is now `medium`** on 3.5 Flash (down from `high` on Gemini 3 Flash Preview). Use `minimal` for speed/simple queries, `low` for code/agentic tasks needing fewer steps (much improved), `medium` for most work, `high` for hard reasoning/math. Never combine `thinking_level` with legacy `thinking_budget` (400 error).
- **Place negative/formatting/quantitative constraints LAST**, after context and the main task. Gemini drops constraints that appear too early.
- **Put the question after the data.** For large inputs (books, codebases, long transcripts), place instructions at the end and anchor with "Based on the preceding information…".
- **Avoid broad negative instructions** like "do not infer/guess" — they make the model refuse basic logic and arithmetic. Instead: "Reason strictly from the provided text; do not introduce external information."
- **Less verbose by default.** For conversational output, steer explicitly ("Explain as a friendly, talkative assistant").
- **Function calling is strict:** every `FunctionResponse` must include the matching `id` and `name`, with exactly one response per call. Put multimodal content and any extra inline instructions INSIDE the function response (append instructions to the response text after two newlines), never as separate parts — otherwise you get thought leakage and lower-quality output.
- **Thought preservation is automatic** across turns; pass the full, unmodified history (including thought signatures) so reasoning context carries forward. The SDKs handle this.
- **To reduce excessive tool calls,** lower the thinking level first, then add a system instruction with an explicit action budget ("You have a limited budget of N tool calls; use them efficiently").
- **No Computer Use** in 3.5 Flash — stay on Gemini 3 Flash Preview for those workloads.

#### Claude Fable 5 / Mythos 5

- **Effort is the primary control: use `high` as the default.** Use `xhigh` for the most capability-sensitive work (complex reasoning, research, high-stakes output). Use `medium` or `low` for routine tasks or when you want a faster, more interactive working style. Even `low` Fable 5 often exceeds `xhigh` Opus 4.8 on prior benchmarks.
- **Adaptive thinking only — no extended thinking budgets.** Set `thinking: {type: "adaptive"}` as on Opus 4.8, but there is no budget parameter. Thinking output is summarized-only; do not instruct the model to echo, transcribe, or explain its internal reasoning as response text — this triggers the `reasoning_extraction` refusal category and causes elevated fallbacks to Opus 4.8.
- **Turns can run for many minutes.** Individual hard tasks can block for minutes; autonomous runs can extend for hours. Adjust client timeouts and streaming indicators before migrating. Consider asynchronous harnesses (scheduled jobs) over blocking patterns.
- **One brief instruction steers an entire behaviour pattern.** Instruction-following is improved enough that you do not need to enumerate each case. For over-elaboration at high effort, a single brevity instruction suffices — for example:
  ```
  Lead with the outcome. Your first sentence should answer "what happened" or "what did you find."
  Supporting detail comes after. To keep output short, be selective (drop details that don't change
  what the reader does next), not terse (no arrow chains, abbreviations, or jargon).
  ```
- **Checkpoint behavior: stop only when genuinely blocked.** To prevent unwanted mid-run pauses, tell it when pausing is legitimate:
  ```
  Pause only when the work requires it: a destructive or irreversible action, a real scope change,
  or input only the user can provide. If you hit one of these, ask and end the turn.
  ```
- **Define explicit scope boundaries.** Fable 5 can take unrequested actions (drafting emails, creating backup branches). State what it should and should not do:
  ```
  When the user is describing a problem or thinking out loud, the deliverable is your assessment —
  report findings and stop. Don't apply a fix until asked. Before running any command that changes
  system state, check that the evidence supports that specific action.
  ```
- **Give the reason, not just the request.** Context about *why* lets Fable 5 connect the task to relevant information rather than inferring intent. Especially for long-running agents: "I'm working on [larger task] for [who]. They need [what the output enables]. With that in mind: [request]."
- **Safety classifiers cover offensive cybersecurity and biology/life sciences.** Benign work in these domains can also trigger them. The response returns `stop_reason: "refusal"`. Configure server-side or client-side fallback to Claude Opus 4.8 for automatic re-routing.
- **Vision is substantially improved.** Fable 5 interprets dense technical images, web apps, and screenshots with higher accuracy and often fewer output tokens. It is trained to use bash and crop tools for flipped, blurry, or noisy images.
- **Older prompts and skills are often too prescriptive.** Prompts written for prior models can degrade Fable 5 output quality. Before deploying an existing prompt, test default performance and remove instructions that are no longer needed.



Always produce exactly three sections:

#### 1. `<Inputs>`
List minimal, non-overlapping variables:
- Use `{$UPPERCASE_NAME}` format
- Each variable appears exactly ONCE in substitution form
- Semantically named for content

#### 2. `<Strategy_Note>`
One sentence: target model, execution mode, and key design choice.

#### 3. `<Instructions>`
Order matters:
1. Role/context (1-2 sentences max)
2. Input variables in XML tags: `<tag>{$VARIABLE}</tag>`
3. Task constraints (what NOT to do) — for Gemini, place these last
4. Output format specification

**For judgment tasks:** Require justification BEFORE scores.

### Anti-Patterns (Chat Mode)

| Avoid | Why |
|-------|-----|
| Numbered rules > 5 items | Structural overhead dominates |
| Redundant restatements | Token waste, hyper-literalism (esp. Opus 4.8) |
| "Think step by step" (reasoning models) | Disrupts internal reasoning in Claude Opus 4.8, GPT-5.5, Gemini 3.x |
| "CRITICAL: You MUST use this tool" (Claude) | Causes overtriggering; use calm language |
| Assuming an instruction generalises (Opus 4.8) | It follows literally; state scope ("every section, not just the first") |
| Process-heavy step lists (GPT-5.5) | Adds noise; prefer outcome-first + stop rules |
| Unnecessary ALWAYS/NEVER/must (GPT-5.5) | Reserve for true invariants; use decision rules otherwise |
| Temperature/top_p/top_k tuning (Gemini 3.x) | Causes looping; remove and use defaults |
| Negative constraints at top (Gemini 3.x) | Gets dropped; place at end, after data |
| Generic "don't use cream" for Opus 4.8 design | Just swaps palette; specify an alternative or ask for options |
| "Show your reasoning" / "explain your thinking" (Fable 5) | Triggers `reasoning_extraction` refusal → elevated fallback to Opus 4.8 |
| Extended thinking budget on Fable 5 | Not supported; adaptive only — no budget parameter |
| Carrying over old prescriptive prompts to Fable 5 | Over-specification degrades output; test defaults first, prune |
| Not adjusting timeouts for Fable 5 | Hard tasks run for minutes; async runs for hours — blocking harnesses will time out |
| Verbose natural language | Formal syntax more efficient |
| Multiple examples when one suffices | Diminishing returns |
| Lengthy preambles | Delays core instruction |

### Effective Patterns (Chat Mode)

| Pattern | When |
|---------|------|
| Explicit XML output tags | Always |
| Conditional logic: "IF [x], THEN [y]" | Branching behavior needed |
| Single canonical example | Non-trivial task |
| Negative constraints (placed last for Gemini) | Known failure modes exist |
| Self-checkable validation criteria | Quality matters |
| Explicit persona + collaboration style (GPT-5.5) | Customer-facing writing |
| Effort/thinking_level calibration note | Performance tuning needed |

### Calibration Checklist (Chat Mode)

Before finalising, verify:

1. **Removability test**: Can any instruction be removed without ambiguity?
2. **Rule count**: More than 5 enumerated rules? Consolidate or convert to prose.
3. **Redundancy check**: Any repeated emphasis? Remove it.
4. **Model-specific check**: Does this prompt violate any target-model anti-patterns?
5. **Token efficiency**: Could this be said in fewer words without losing clarity?
6. **Constraint ordering** (Gemini 3.x): Are negative constraints at the end, after the data?
7. **Tool-use tone** (Claude): Is triggering language calm, not aggressive?
8. **Scope is explicit** (Opus 4.8): Does any instruction that should apply broadly say so, rather than relying on the model to generalise?
9. **Reasoning echo removed** (Fable 5): Does the prompt avoid asking the model to reproduce, echo, or explain its internal reasoning?
10. **Effort level set** (Fable 5): Is effort specified? `high` for most tasks, `xhigh` for capability-sensitive, `medium`/`low` for routine/fast.
11. **Fallback configured** (Fable 5): If cybersecurity or biology content is possible, is `stop_reason: "refusal"` handled with a fallback to Opus 4.8?

---

## Autonomous Mode

When the prompt will drive an agent that runs unattended, the over-specification penalty inverts: **under-specification is the primary failure mode.** The agent cannot ask for clarification mid-run. A missing constraint or ambiguous objective propagates silently through dozens of steps.

Autonomous prompts are built across four layers. Each layer addresses a different failure mode. All four must be present for reliable autonomous execution.

### Layer 1: Prompt Craft (Clear Instructions)

Same model-specific guidance applies to the *phrasing* of instructions. But clear phrasing alone cannot compensate for missing context, ambiguous intent, or absent structure.

### Layer 2: Context Engineering (Make It Plausibly Solvable)

Design what the agent can see so the task is solvable without additional information.

**Context audit questions (answer all before writing the prompt):**

1. **Data**: What documents, records, examples, or reference material does the agent need?
2. **State**: What is the current state of the project/task?
3. **Tools**: What tools, APIs, or capabilities does the agent have access to?
4. **Definitions**: Are domain terms, abbreviations, or conventions defined?
5. **Examples**: Is there a prior example of good output?
6. **Absence test**: Can the agent proceed correctly without asking a single follow-up question?

### Layer 3: Intent Engineering (Encode the "Why" and Decision Rights)

Communicate goals, tradeoffs, and autonomy boundaries so the agent stays aligned over long runs.

**Intent specification components:**

1. **Objective**: The outcome, not activities.
2. **Success signal**: Measurable indicator.
3. **Value hierarchy**: When objectives conflict, what wins?
4. **Decision boundaries**: Autonomous vs. must-escalate.
5. **Escalation triggers**: Conditions to stop and surface.
6. **Failure modes**: Explicit unacceptable outcomes.
7. **Tradeoff resolution**: Default when goals compete.

### Layer 4: Specification Engineering (Executable Contract)

Turn the work into a structured document the agent can execute against without further guidance.

**Specification components:**

1. **Acceptance criteria**: What must be true for the output to be done?
2. **Constraint architecture**: Hard (must/must-not) vs. soft (should/ideally).
3. **Task decomposition**: Discrete steps with dependencies.
4. **Evaluation criteria**: How to check each step.
5. **Definition of done**: Concrete, verifiable end state.
6. **Definition of wrong**: Failed outcomes even if steps "completed."
7. **Edge cases**: IF [condition] THEN [response].

### Model-Specific Autonomous Blocks

For autonomous agents, include these structured blocks adapted to the target model. These are proven patterns from vendor documentation — include the ones relevant to the task.

#### Verification Loop (all models, essential for high-impact actions)

```
<verification_loop>
Before finalising:
- Check correctness: does the output satisfy every requirement?
- Check grounding: are factual claims backed by provided context or tool outputs?
- Check formatting: does the output match the requested schema or style?
- Check safety and irreversibility: if the next step has external side effects, ask permission first.
</verification_loop>
```

#### Completeness Contract (GPT-5.5 pattern, useful for all models)

```
<completeness_contract>
- Treat the task as incomplete until all requested items are covered or explicitly marked [blocked].
- Keep an internal checklist of required deliverables.
- For lists, batches, or paginated results: determine expected scope, track processed items, confirm coverage before finalising.
- If any item is blocked by missing data, mark it [blocked] and state exactly what is missing.
</completeness_contract>
```

#### Tool Persistence Rules (critical for agentic workflows)

```
<tool_persistence_rules>
- Use tools whenever they materially improve correctness, completeness, or grounding.
- Do not stop early when another tool call would materially improve the result.
- Keep calling tools until the task is complete AND verification passes.
- If a tool returns empty or partial results, retry with a different strategy.
</tool_persistence_rules>
```

#### Dependency Checks (GPT-5.5 pattern, prevents skipped prerequisites)

```
<dependency_checks>
- Before taking an action, check whether prerequisite discovery, lookup, or retrieval steps are required.
- Do not skip prerequisites just because the intended final action seems obvious.
- If the task depends on the output of a prior step, resolve that dependency first.
</dependency_checks>
```

#### Empty-Result Recovery (prevents premature "not found" conclusions)

```
<empty_result_recovery>
If a lookup returns empty, partial, or suspiciously narrow results:
- Do not immediately conclude that no results exist.
- Try at least 1-2 fallback strategies (alternate query wording, broader filters, alternate source/tool).
- Only then report that no results were found, along with what you tried.
</empty_result_recovery>
```

#### Research Mode (for research, review, and synthesis tasks)

```
<research_mode>
Do research in 3 passes:
1) Plan: list 3-6 sub-questions to answer.
2) Retrieve: search each sub-question and follow 1-2 second-order leads.
3) Synthesise: resolve contradictions and write the final answer with citations.
Stop only when more searching is unlikely to change the conclusion.
</research_mode>
```

#### Citation and Grounding Rules (critical for research agents)

```
<citation_rules>
- Only cite sources retrieved in the current workflow.
- Never fabricate citations, URLs, IDs, or quote spans.
- Use exactly the citation format required by the host application.
- Attach citations to the specific claims they support, not only at the end.
</citation_rules>

<grounding_rules>
- Base claims only on provided context or tool outputs.
- If sources conflict, state the conflict explicitly and attribute each side.
- If the context is insufficient, narrow the answer or say you cannot support the claim.
- If a statement is an inference rather than a directly supported fact, label it as such.
</grounding_rules>
```

#### Missing Context Gate (prevents hallucination under uncertainty)

```
<missing_context_gating>
- If required context is missing, do NOT guess.
- Prefer the appropriate lookup tool when the missing context is retrievable.
- Ask a minimal clarifying question only when lookup is not possible.
- If you must proceed, label assumptions explicitly and choose a reversible action.
</missing_context_gating>
```

#### Parallel Tool Calling (when independent work can run simultaneously)

```
<parallel_tool_calling>
- When multiple retrieval or lookup steps are independent, prefer parallel tool calls.
- Do not parallelise steps that have prerequisite dependencies.
- After parallel retrieval, pause to synthesise before making more calls.
</parallel_tool_calling>
```

#### User Updates (for agents with progress reporting)

```
<user_updates_spec>
- Only update the user when starting a new major phase or when something changes the plan.
- Each update: 1 sentence on outcome + 1 sentence on next step.
- Do not narrate routine tool calls.
</user_updates_spec>
```

#### Fable 5: Memory System (recommended for long-running or multi-session agents)

```
<memory_system>
Maintain a memory file at [PATH]. For each lesson learned or confirmed approach:
- One file per lesson; one-line summary at the top.
- Record corrections and confirmed approaches alike, including why they mattered.
- Do not save what the repo or chat history already records.
- Update an existing note rather than creating a duplicate.
- Delete notes that turn out to be wrong.
Reference [PATH] at the start of each run.
</memory_system>
```

#### Fable 5: Progress Audit (prevents fabricated status reports on long runs)

```
<progress_audit>
Before reporting progress, audit each claim against a tool result from this session.
Only report work you can point to evidence for; if something is not yet verified, say so
explicitly. Report outcomes faithfully: if tests fail, say so with the output; if a step
was skipped, say that; when something is done and verified, state it plainly without hedging.
</progress_audit>
```

#### Fable 5: Scope Boundary (prevents unrequested side-effects)

```
<scope_boundary>
When the user is describing a problem, asking a question, or thinking out loud rather than
requesting a change, the deliverable is your assessment. Report your findings and stop.
Do not apply a fix until explicitly asked.
Before running any command that changes system state (restarts, deletes, config edits),
check that the evidence actually supports that specific action. A signal that pattern-matches
to a known failure may have a different cause.
Do not create backups, send messages, or draft content that was not requested.
</scope_boundary>
```

#### Fable 5: Autonomous Communication Style (for async runs where the user was not watching)

```
<async_communication_style>
Terse shorthand between tool calls is fine — that is you thinking out loud.
Your final summary is different: it is for a reader who did not see any of your working.
Write it as a re-grounding, not a continuation:
- Outcome first: one sentence on what happened or what you found.
- Then the one or two things you need from them, each explained as if new.
- Drop working shorthand, arrow chains, hyphen-stacked compounds, and labels you made up.
- Write complete sentences. Spell out terms. Give each file, commit, or flag its own plain-language clause.
- If you have to choose between short and clear, choose clear.
</async_communication_style>
```

#### Fable 5: Autonomous Continuation Guard (prevents early stopping)

```
<continuation_guard>
You are operating autonomously. The user is not watching in real time.
For reversible actions that follow from the original request, proceed without asking.
Before ending your turn, check your last paragraph: if it is a plan, a list of next steps,
or a promise about work you have not done ("I'll…", "let me know…"), do that work now
with tool calls. End your turn only when the task is complete or you are genuinely blocked
on input only the user can provide.
</continuation_guard>
```

#### Fable 5: Context Budget Reassurance (prevents premature session handoff on long runs)

```
<context_budget_reassurance>
You have ample context remaining. Do not stop, summarize, or suggest a new session on
account of context limits. Continue the work.
</context_budget_reassurance>
```



Produce four sections:

#### 1. `<Inputs>`
Same format as chat mode, but include context sources (files, data, tools) as variables.

#### 2. `<Strategy_Note>`
One sentence: execution mode, target model, and key design choice.

#### 3. `<Context_Design>`
Enumerate what the agent needs access to and verify completeness:
- Data sources and their format/location
- Tool access and descriptions
- State information
- Domain definitions or glossary
- Reference examples of good output

#### 4. `<Agent_Spec>`
Structured specification:

```
## Objective
[Outcome, not activity]

## Success Signal
[Measurable indicator]

## Value Hierarchy
[Ordered priorities when objectives conflict]

## Decision Boundaries
- Autonomous: [what the agent can decide alone]
- Escalate: [what the agent must flag/skip/surface]

## Constraints
### Hard (must/must-not)
- [constraint]
### Soft (should/prefer)
- [preference]

## Task Decomposition
1. [Step/phase] → [expected output] → [dependency]
2. ...

## Structured Blocks
[Include relevant blocks from the Model-Specific Autonomous Blocks section above]

## Acceptance Criteria
- [criterion 1]
- [criterion 2]

## Definition of Done
[Concrete end state]

## Definition of Wrong
[Unacceptable outcomes, even if steps "completed"]

## Edge Cases
- IF [condition] THEN [response]
```

### Thinking/Reasoning Configuration Guide

When the prompt needs to specify reasoning behaviour, use this reference:

| Model | Parameter | Recommended Defaults |
|-------|-----------|---------------------|
| Claude Fable 5 / Mythos 5 | `thinking: {type: "adaptive"}` (no budget); summarized-only output | effort=high for most tasks; xhigh for capability-sensitive work; medium/low for routine or interactive; never instruct to echo/reproduce thinking |
| Claude Opus 4.8 | `thinking: {type: "adaptive"}` (OFF unless set) + `effort` | effort=xhigh for coding/agentic, min high for intelligence-sensitive, medium/low for fast scoped work; max only if evals justify (can overthink) |
| Claude Sonnet 4.6 / Haiku 4.5 | `thinking: {type: "adaptive"}` + `effort` | Sonnet defaults to effort=high; use medium for most, low for latency-sensitive; set a large max_tokens at medium+/high |
| GPT-5.5 | `reasoning_effort` | none for execution; low for instruction-following; medium for research/synthesis; re-evaluate low/medium before high/xhigh (5.5 reasons more efficiently) |
| Gemini 3.1 Pro | `thinking_level` | high (default) for reasoning; low for chat/throughput; medium for balanced |
| Gemini 3.5 Flash | `thinking_level` | medium (default) for most; low for code/agentic with fewer steps; minimal for speed; high for hard reasoning |
| Gemini 3.1 Flash-Lite | `thinking_level` | minimal (default) for high-volume/low-cost; low for light reasoning |

**Rule of thumb**: Before raising reasoning effort, first add verification loops, completeness contracts, and tool persistence rules. Prompt improvements are cheaper and often more effective than reasoning budget increases.

### Anti-Patterns (Autonomous Mode)

| Avoid | Why |
|-------|-----|
| Relying on phrasing alone | Agent can't ask for clarification; missing structure causes silent failure |
| Omitting decision boundaries | Agent makes autonomous calls you didn't authorise |
| No definition of "wrong" | Agent completes incorrectly and reports success |
| Vague success criteria | No way to evaluate whether the run worked |
| Assuming the agent knows context | If it's not in the spec, it doesn't exist |
| Flat constraint lists without priority | Agent can't resolve conflicts |
| No edge case handling | First unexpected input derails the run |
| No verification loop before irreversible actions | Catches requirement misses and format drift |
| No empty-result recovery | Agent concludes "not found" on first failed search |
| No dependency checks | Agent skips prerequisite steps because end state seems obvious |

### Calibration Checklist (Autonomous Mode)

Before finalising, verify:

1. **Absence test**: Could a competent human follow this spec without asking questions?
2. **Context completeness**: Is every piece of information the agent needs loaded or referenced?
3. **Intent clarity**: Could someone read the objective and success signal and know exactly what "good" looks like?
4. **Decision boundary coverage**: For every judgment call, is there guidance on proceed vs. escalate?
5. **Failure mode coverage**: Have you listed what "wrong" looks like?
6. **Constraint priority**: If two constraints conflict, does the spec resolve which wins?
7. **Decomposition test**: Is each step small enough that failure is detectable before it propagates?
8. **Structured blocks**: Are the relevant autonomous blocks included (verification, completeness, tool persistence, etc.)?
9. **Model-specific check**: Does this spec respect the target model's anti-patterns?
10. **Thinking/reasoning config**: Is the recommended reasoning effort documented for the deployment?

---

## When Improving Existing Prompts

1. **Determine execution mode** — chat or autonomous?
2. **Identify target model** — different models have different failure modes.
3. If **chat**: check for model-specific anti-patterns, measure if 30% of tokens could be removed, consolidate rules, add output format if missing, remove hedging language. For Claude, dial back aggressive tool-triggering language and make broad-scope instructions explicit (Opus 4.8 takes them literally). For GPT-5.5, convert process steps into outcome-first goals with stop rules. For Gemini 3.x, move negative constraints to the end.
4. If **autonomous**: run the Absence Test first. Then check for missing context, unspecified intent, absent acceptance criteria, undefined failure modes, and missing structured blocks (verification loop, completeness contract, etc.). **Add structure before removing words.**
5. If **migrating between models**: translate model-specific patterns using the guidance above. Common migrations:
   - GPT-5.x → Claude Opus 4.8: Remove `developer` role, use `system`; replace `<output_contract>` blocks with XML-structured instructions; remove prefill patterns; map reasoning_effort to effort (and remember thinking is OFF unless you set adaptive); make any broad-scope instruction explicit, since Opus 4.8 won't generalise it.
   - Claude → GPT-5.5: Shift from process steps to outcome-first goals with stop rules; add explicit output contracts and a follow-through policy; convert ALWAYS/NEVER lists into decision rules; add a reasoning_effort recommendation and `text.verbosity`.
   - Either → Gemini 3.5 Flash: Remove temperature/top_p/top_k; move negative constraints and the question to the end (after data); set `thinking_level` (default medium); ensure thought signatures and matching id/name are circulated in tool-calling chains; put multimodal content inside function responses.
   - Any model → Fable 5: (1) Remove any extended thinking budget — adaptive only, no budget parameter; (2) Audit for instructions that tell the model to echo, reproduce, or explain its reasoning — remove them (triggers `reasoning_extraction` refusal); (3) Adjust harness timeouts and streaming — hard tasks can block for minutes; (4) Review and prune old prescriptive instructions — default performance is often better without them; (5) Configure fallback to Opus 4.8 for `stop_reason: "refusal"` (cybersecurity/biology domains); (6) Add Fable 5-specific autonomous blocks (progress audit, scope boundary, memory system, continuation guard) for long-running agents; (7) Replace effort=max or effort=xhigh defaults with effort=high unless the task genuinely needs peak capability.
6. For prompts that are **ambiguously scoped**: ask the user. The design strategy differs fundamentally.

---

## Personality and Writing Control Patterns

For customer-facing prompts that need specific voice, use this separation pattern (works across all models, explicitly supported by GPT-5.5's personality/collaboration split and Claude Opus 4.8):

```
<personality_and_writing_controls>
- Persona: [one sentence defining who this is]
- Channel: [Slack / email / memo / PRD / blog]
- Emotional register: [direct/calm/energised/etc.] + "not [overdo this]"
- Formatting: [ban bullets/headers/markdown if you want prose]
- Length: [hard limit, e.g. <=150 words or 3-5 sentences]
- Default follow-through: if the request is clear and low-risk, proceed without asking.
</personality_and_writing_controls>
```

Personality should not override task-specific output requirements. If the user asks for JSON, return JSON regardless of persona.

---

## Quick Reference: Cross-Model Prompt Translation

| Concept | Claude Fable 5 / Mythos 5 | Claude Opus 4.8 | GPT-5.5 | Gemini 3.5 Flash |
|---------|--------------------------|-----------------|---------|------------------|
| Reasoning depth | `effort` (high/xhigh default; no max) | `effort` (low/medium/high/xhigh/max) | `reasoning_effort` (none/low/medium/high/xhigh) | `thinking_level` (minimal/low/medium/high) |
| Thinking mode | Adaptive; summarized-only output; **no extended budget** | Adaptive, but OFF unless `thinking:{type:"adaptive"}` is set | Built-in; tuned via reasoning_effort | Dynamic; default level now `medium`; preserved across turns |
| Default depth | high for most tasks; xhigh for capability-sensitive | xhigh for coding/agentic, high for intelligence-sensitive | re-evaluate low/medium before escalating | medium |
| Sampling (temp/top_p/top_k) | Default fine | Default fine | Default fine | Remove — use defaults |
| Instruction following | Brief instructions steer whole patterns; no over-enumeration | Literal; state scope explicitly | Outcome-first; decision rules over absolute rules | Concise; question/constraints last |
| Reasoning reproduction | **Never instruct** (triggers `reasoning_extraction` refusal) | Avoid; use XML outputs instead | Avoid; use output contracts | Avoid |
| Structured output | XML tags, structured outputs API | XML tags, structured outputs API | XML blocks, output contracts, `text.verbosity` | JSON schema, structured outputs |
| Tool-use tone | Calm; effort=high increases tool use | Calm; raise effort for more tool use | Explicit persistence + stop rules + retrieval budgets | Action budget; lower thinking_level to reduce calls |
| Constraint placement | Anywhere (XML-wrapped) | Anywhere (XML-wrapped) | Named XML blocks | Negative constraints LAST, after data |
| Long sessions | Memory system + progress audit + context-budget reassurance | Context awareness + memory tool | phase param + compaction | Automatic thought preservation |
| Safety refusals | Cybersecurity + biology → `stop_reason:"refusal"`; configure Opus 4.8 fallback | None (standard) | None (standard) | None (standard) |
| Parallel subagents | Dispatched readily; prefer async comms | Spawns fewer; needs explicit instruction | Standard | Standard |
| Prefill/continuation | Not supported on last turn | Not supported on last turn | Standard | Thought signatures required for function calling |
| Function-call matching | Standard | Standard | Standard | Strict: id + name + count must match |
| Unrequested actions | Add scope boundary instruction | Minimal risk | Minimal risk | Minimal risk |
| Long-run output style | Async communication style block needed for legibility | Standard | Standard | Standard |
