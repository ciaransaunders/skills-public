# DRACO Research Prompt Generator

Transform vague research requests into prompts that maximise deep research agent performance, based on the DRACO benchmark methodology.

## Core Principle: The Three Pillars

Every generated prompt MUST satisfy all three criteria simultaneously:

### 1. OBJECTIVITY
Clear, measurable success criteria where multiple experts would converge on what constitutes a correct answer.

**Indicators of objectivity:**
- Specific metrics, dates, or verifiable facts
- Named authoritative sources
- Documented information vs. opinions
- Deterministic outcomes

**Red flags (make subjective):**
- "Best", "should", "compelling", "interesting"
- Speculative future predictions
- Undefined quality judgments

### 2. BOUNDED/CONSTRAINED
Natural limits preventing endless expansion. Clear point where a complete answer has been given.

**Good constraints:**
- Specific number limits with objective ranking metrics ("top 5 by AUM", "3 most-cited papers")
- Time constraints ("2022–2025", "since Q3 2024")
- Geographic/domain limits ("in the EU", "for e-commerce")
- Named entities (not "top approaches" but "LoRA, full fine-tuning, and instruction tuning")

**Pseudo-constraints to AVOID:**
- Ungrounded "Top N" without objective metric ("top 5 challenges" — subjective)
- "Cite at least N sources" (different experts choose different sources)
- "Multiple studies" without naming which ones

### 3. CHALLENGING
Difficulty from complexity or synthesis, NOT from volume or tedium.

**Good challenge sources:**
- Synthesis across multiple named sources
- Multi-step reasoning or analysis
- Finding specific hard-to-locate information
- Domain expertise requirements

**Bad (tedious, not challenging):**
- Listing many items without analysis
- Kitchen-sink queries with 8+ deliverables
- Simple factual recall

---

## The Six Augmentation Dimensions

Apply systematically to transform vague queries:

### CONTEXT Dimensions

| Dimension | Action | Example |
|-----------|--------|---------|
| **Persona** | Add professional role context | "As a buy-side analyst conducting due diligence..." |
| **Output** | Specify deliverable format | "financial analysis research report", "comparative brief" |
| **Source** | Add retrieval specificity | "Pull from SEC proxy statements", "based on WHO GLASS reports" |

### SCOPE Dimensions

| Dimension | Action | Example |
|-----------|--------|---------|
| **Temporal** | Expand/bound time scope | "NVIDIA financials" → "NVIDIA financials 2022–2025" |
| **Cross-entity** | Add comparative requirements | "CEO compensation at Google" → "CEO compensation at Google, Meta, and Apple" |
| **Geography** | Expand/specify geographic scope | "AI landscape" → "global AI landscape, focusing on US and China" |

---

## Prompt Generation Workflow

### Step 1: Parse the Request
Identify:
- Core research question
- Implicit constraints (if any)
- Domain/subject area
- Apparent depth requirement

### Step 2: Apply Augmentation Checklist

For each dimension, determine if augmentation is needed:

```
□ PERSONA: Does specifying a professional role add clarity?
□ OUTPUT: Is the expected deliverable format clear?
□ SOURCE: Should specific authoritative sources be named?
□ TEMPORAL: Is timeframe bounded appropriately?
□ CROSS-ENTITY: Would comparison improve depth?
□ GEOGRAPHY: Is geographic scope clear and appropriate?
```

### Step 3: Validate Three Pillars

Before finalising, verify:
```
□ OBJECTIVITY: Would two experts agree on what counts as correct?
□ BOUNDED: Is there a clear stopping point? Are "Top N" grounded?
□ CHALLENGE: Does difficulty come from synthesis, not volume?
□ DELIVERABLES: 3–5 focused items maximum, not kitchen-sink
```

### Step 4: Structure the Output

Organise the prompt following this pattern:

```
[PERSONA CONTEXT if applicable]

[CORE RESEARCH QUESTION with all constraints]

[SPECIFIC REQUIREMENTS numbered 1–4, maximum 5]

[SOURCE SPECIFICITY — named authoritative sources]

[OUTPUT SPECIFICATION — format, structure expectations]
```

---

## Output Format

Present generated prompts in a clean, copy-ready format:

```
## Generated Research Prompt

[The optimised prompt]

---

### Augmentations Applied
- [List which dimensions were added]

### Validation Notes
- Objectivity: [How verified]
- Boundedness: [Constraints applied]
- Challenge: [Source of complexity]
```

---

## Anti-Patterns to Avoid

| ❌ Avoid | Why | ✓ Instead |
|----------|-----|-----------|
| "Top 5 challenges" | Subjective ranking | "Top 5 challenges by regulatory citation frequency" |
| "Cite at least 3 studies" | Non-deterministic | "Compare findings from [Study A] and [Study B]" |
| "Analyse all aspects of X" | Unbounded | "Analyse [specific aspect 1], [aspect 2], [aspect 3]" |
| "What's the best approach" | Subjective | "Compare approaches A, B, C on metrics X, Y, Z" |
| Lists of 8+ deliverables | Volume, not challenge | Focus on 3–5 core elements |
| "Recent research shows" | Vague temporality | "Research published 2023–2025 shows" |

---

## Example Transformations

**Before:** "Research private credit funds"

**After:** "Identify the top 5 private credit funds by AUM in North America as of Q4 2025. For each fund, document: (1) minimum investment threshold, (2) management fee structure, (3) target IRR range, and (4) primary sector focus. Source data from fund prospectuses, Preqin, and SEC filings."

---

**Before:** "How is AI affecting healthcare?"

**After:** "Compare how computer vision systems have been adapted for automated breast cancer detection in mammography across FDA-cleared products (2020–2025). Report: (1) sensitivity/specificity thresholds required by FDA and EU MDR, (2) clinical trial results from at least 3 named trials, (3) current deployment scale in US hospital systems. Prioritise sources: FDA 510(k) clearance documents, peer-reviewed clinical validation studies, and manufacturer regulatory submissions."

---

See `references/transformation-examples.md` for more domain-specific examples.
See `references/domain-patterns.md` for domain-specific augmentation templates.
See `references/validation-checklist.md` for the full quality validation checklist.
