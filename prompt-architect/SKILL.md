# Prompt Architect

Create and optimise reusable prompt templates for AI assistants and autonomous agents. You are NOT completing the task — you are writing instructions another AI will execute.

Use this skill when someone asks to make a prompt for a task, improve an existing prompt, write a prompt template, or asks about prompting best practices for any LLM.

---

## Step 0: Determine Execution Mode and Target Model

Before anything else, determine two things. Ask the user OR infer from context:

### A. Execution Mode

| Mode | Signal | Design Strategy |
|------|--------|-----------------|
| **Chat** | Single-turn or back-and-forth; human in the loop; quick iteration | Minimise specification. Over-specification penalty applies. |
| **Autonomous** | Agent runs unattended; multi-step; no mid-run correction | Maximise specification. Under-specification causes silent failure. |

### B. Target Model

| Model Family | Key Prompt Characteristics |
|-------------|---------------------------|
| **Claude 4.6** (Opus/Sonnet) | Adaptive thinking; very proactive tool use — dial *back* aggressive triggering language; XML tags preferred; concise by default |
| **GPT-5.4** | Reasoning effort knob (none/low/medium/high/xhigh); XML-block structured prompts; explicit output contracts; strong personality adherence |
| **Gemini 3.1 Pro** | Dynamic thinking (thinking_level: minimal/low/medium/high); temperature MUST stay at 1.0; negative constraints placed LAST; less verbose by default |
| **Gemini 3 Flash** | Same thinking_level system; minimal level available; optimised for speed/cost |
| **Unknown/Generic** | Constraint-based, minimal scaffolding, XML structure |

**This distinction governs everything below.** Chat-mode prompts and autonomous-agent specifications are different design problems with opposite failure modes.

---

## Chat Mode

### Core Principle: Specification Threshold

Quality follows a non-monotonic curve. Below threshold S* ≈ 0.5, detail improves performance. Beyond it, additional specification causes degradation ("cognitive leakage"). Aim for maximum clarity with minimum structural overhead.

### Model-Specific Chat Guidance

#### Claude 4.6

- **Do NOT over-prompt tool use.** Claude 4.6 is proactive. Replace "CRITICAL: You MUST use this tool when..." with "Use this tool when...". Over-triggering language will cause overtriggering.
- **Adaptive thinking is the default.** Do not include "think step by step" — Claude decides when to think.
- **Effort parameter replaces budget_tokens.** Steer depth via effort (low/medium/high/max).
- **No prefilled responses.** Use structured outputs, XML tags, or direct instructions to control format instead.
- **Concise by default.** If you want verbose output, ask explicitly.
- **Prefer telling Claude what TO do** rather than what not to do.
- **XML tags strongly preferred** for structuring inputs, outputs, and instructions.

#### GPT-5.4

- **Use XML-block structured prompts.** GPT-5.4 excels with named blocks like `<output_contract>`, `<verbosity_controls>`, `<tool_persistence_rules>`.
- **Define an explicit output contract:**
  ```
  <output_contract>
  - Return exactly the sections requested, in the requested order.
  - If a format is required (JSON, Markdown, SQL, XML), output only that format.
  - Apply length limits only to the section they are intended for.
  </output_contract>
  ```
- **Reasoning effort is a last-mile knob.** Default to none for execution tasks, low for instruction-following, medium for research/synthesis. Always try stronger prompts before raising reasoning effort.
- **Explicit follow-through policy:**
  ```
  <default_follow_through_policy>
  - If the user's intent is clear and the next step is reversible and low-risk, proceed.
  - Ask permission only if the step is irreversible, has external side effects, or requires missing sensitive information.
  </default_follow_through_policy>
  ```

#### Gemini 3 / 3.1 Pro

- **NEVER change temperature from 1.0.** Lower values cause looping and degraded performance.
- **Place negative constraints LAST.** Structure prompts as: [Context] → [Main task] → [Negative/formatting/quantitative constraints].
- **Do not use broad negative instructions** like "do not infer" or "do not guess" — these cause the model to refuse basic logic. Instead: "Perform calculations and logical deductions based strictly on the provided text. Do not introduce external information."
- **For grounding:** "The provided context is the only source of truth for this session."
- **thinking_level controls reasoning depth.** Use low for latency-sensitive tasks, high for complex reasoning.

### Chat Output Structure

Always produce exactly three sections:

#### 1. `<Inputs>`
List minimal, non-overlapping variables:
- Use `{$UPPERCASE_NAME}` format
- Each variable appears exactly ONCE in substitution form

#### 2. `<Strategy_Note>`
One sentence: target model, execution mode, and key design choice.

#### 3. `<Instructions>`
Order matters:
1. Role/context (1–2 sentences max)
2. Input variables in XML tags: `<tag>{$VARIABLE}</tag>`
3. Task constraints (what NOT to do) — for Gemini, place these last
4. Output format specification

**For judgment tasks:** Require justification BEFORE scores.

### Anti-Patterns (Chat Mode)

| Avoid | Why |
|-------|-----|
| Numbered rules > 5 items | Structural overhead dominates |
| Redundant restatements | Token waste, hyper-literalism |
| "Think step by step" (reasoning models) | Disrupts internal reasoning |
| "CRITICAL: You MUST use this tool" (Claude 4.6) | Causes overtriggering |
| Temperature tuning (Gemini 3) | Causes looping; leave at 1.0 |
| Negative constraints at top (Gemini 3) | Gets dropped; place at end |
| Lengthy preambles | Delays core instruction |

### Calibration Checklist (Chat Mode)

1. **Removability test**: Can any instruction be removed without ambiguity?
2. **Rule count**: More than 5 enumerated rules? Consolidate or convert to prose.
3. **Redundancy check**: Any repeated emphasis? Remove it.
4. **Model-specific check**: Does this prompt violate any target-model anti-patterns?
5. **Token efficiency**: Could this be said in fewer words without losing clarity?
6. **Constraint ordering** (Gemini): Are negative constraints at the end?
7. **Tool-use tone** (Claude 4.6): Is triggering language calm, not aggressive?

---

## Autonomous Mode

When the prompt will drive an agent that runs unattended, the over-specification penalty inverts: **under-specification is the primary failure mode.** The agent cannot ask for clarification mid-run. A missing constraint or ambiguous objective propagates silently through dozens of steps.

Autonomous prompts are built across four layers. All four must be present for reliable autonomous execution.

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

---

## Reusable Structured Blocks for Autonomous Agents

Include the relevant blocks from this library in any autonomous specification.

### Verification Loop (essential for high-impact actions)

```
<verification_loop>
Before finalising:
- Check correctness: does the output satisfy every requirement?
- Check grounding: are factual claims backed by provided context or tool outputs?
- Check formatting: does the output match the requested schema or style?
- Check safety and irreversibility: if the next step has external side effects, ask permission first.
</verification_loop>
```

### Completeness Contract

```
<completeness_contract>
- Treat the task as incomplete until all requested items are covered or explicitly marked [blocked].
- Keep an internal checklist of required deliverables.
- For lists, batches, or paginated results: determine expected scope, track processed items, confirm coverage before finalising.
- If any item is blocked by missing data, mark it [blocked] and state exactly what is missing.
</completeness_contract>
```

### Tool Persistence Rules

```
<tool_persistence_rules>
- Use tools whenever they materially improve correctness, completeness, or grounding.
- Do not stop early when another tool call would materially improve the result.
- Keep calling tools until the task is complete AND verification passes.
- If a tool returns empty or partial results, retry with a different strategy.
</tool_persistence_rules>
```

### Dependency Checks

```
<dependency_checks>
- Before taking an action, check whether prerequisite discovery, lookup, or retrieval steps are required.
- Do not skip prerequisites just because the intended final action seems obvious.
- If the task depends on the output of a prior step, resolve that dependency first.
</dependency_checks>
```

### Empty-Result Recovery

```
<empty_result_recovery>
If a lookup returns empty, partial, or suspiciously narrow results:
- Do not immediately conclude that no results exist.
- Try at least 1-2 fallback strategies (alternate query wording, broader filters, alternate source/tool).
- Only then report that no results were found, along with what you tried.
</empty_result_recovery>
```

### Research Mode

```
<research_mode>
Do research in 3 passes:
1) Plan: list 3-6 sub-questions to answer.
2) Retrieve: search each sub-question and follow 1-2 second-order leads.
3) Synthesise: resolve contradictions and write the final answer with citations.
Stop only when more searching is unlikely to change the conclusion.
</research_mode>
```

### Citation and Grounding Rules

```
<citation_rules>
- Only cite sources retrieved in the current workflow.
- Never fabricate citations, URLs, IDs, or quote spans.
- Attach citations to the specific claims they support, not only at the end.
</citation_rules>

<grounding_rules>
- Base claims only on provided context or tool outputs.
- If sources conflict, state the conflict explicitly and attribute each side.
- If the context is insufficient, narrow the answer or say you cannot support the claim.
- If a statement is an inference rather than a directly supported fact, label it as such.
</grounding_rules>
```

### Missing Context Gate

```
<missing_context_gating>
- If required context is missing, do NOT guess.
- Prefer the appropriate lookup tool when the missing context is retrievable.
- Ask a minimal clarifying question only when lookup is not possible.
- If you must proceed, label assumptions explicitly and choose a reversible action.
</missing_context_gating>
```

### Parallel Tool Calling

```
<parallel_tool_calling>
- When multiple retrieval or lookup steps are independent, prefer parallel tool calls.
- Do not parallelise steps that have prerequisite dependencies.
- After parallel retrieval, pause to synthesise before making more calls.
</parallel_tool_calling>
```

### User Updates

```
<user_updates_spec>
- Only update the user when starting a new major phase or when something changes the plan.
- Each update: 1 sentence on outcome + 1 sentence on next step.
- Do not narrate routine tool calls.
</user_updates_spec>
```

---

## Autonomous Output Structure

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
[Include relevant blocks from the library above]

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

---

## Reasoning/Thinking Configuration Reference

| Model | Parameter | Recommended Defaults |
|-------|-----------|---------------------|
| Claude 4.6 Opus | `thinking: {type: "adaptive"}` + `effort` | effort=high for complex, medium for most, low for fast |
| Claude 4.6 Sonnet | `thinking: {type: "adaptive"}` + `effort` | effort=medium for most; low for latency-sensitive |
| GPT-5.4 | `reasoning_effort` | none for execution; low for instruction-following; medium for research; high for long-horizon agents |
| Gemini 3.1 Pro | `thinking_level` | high for reasoning; low for chat/throughput; medium for balanced |
| Gemini 3 Flash | `thinking_level` | minimal for speed; low for light reasoning |

**Rule of thumb**: Before raising reasoning effort, first add verification loops, completeness contracts, and tool persistence rules. Prompt improvements are cheaper and often more effective than reasoning budget increases.

---

## Anti-Patterns (Autonomous Mode)

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

---

## When Improving Existing Prompts

1. **Determine execution mode** — chat or autonomous?
2. **Identify target model** — different models have different failure modes.
3. If **chat**: check for model-specific anti-patterns, measure if 30% of tokens could be removed, consolidate rules, add output format if missing. For Claude 4.6, dial back aggressive tool-triggering language. For Gemini, move negative constraints to end.
4. If **autonomous**: run the Absence Test first. Then check for missing context, unspecified intent, absent acceptance criteria, undefined failure modes, and missing structured blocks.
5. If **migrating between models**:
   - GPT-5.x → Claude 4.6: Remove `developer` role, use `system`; replace `<output_contract>` blocks with XML-structured instructions; remove prefill patterns.
   - Claude → GPT-5.4: Add explicit output contracts and follow-through policies; wrap constraints in named XML blocks; add reasoning_effort recommendation.
   - Either → Gemini 3: Lock temperature at 1.0; move negative constraints to end; add thinking_level.
6. For prompts that are **ambiguously scoped**: ask the user. The design strategy differs fundamentally.

---

## Cross-Model Quick Reference

| Concept | Claude 4.6 | GPT-5.4 | Gemini 3.1 |
|---------|-----------|---------|------------|
| Reasoning depth | `effort` (low/medium/high/max) | `reasoning_effort` (none/low/medium/high/xhigh) | `thinking_level` (minimal/low/medium/high) |
| Temperature | Default fine | Default fine | MUST be 1.0 |
| Structured output | XML tags | XML blocks, output contracts | JSON schema |
| Tool-use tone | Calm, normal language | Explicit persistence rules | Standard |
| Constraint placement | Anywhere (XML-wrapped) | Named XML blocks | Negative constraints LAST |
| Prefill/continuation | Not supported on last turn | Standard | Thought signatures required |

---

## Personality and Voice Control (Customer-Facing Prompts)

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
