# Research Prompt Validation Checklist

Use this checklist to verify generated prompts meet DRACO quality standards.

## Pillar 1: OBJECTIVITY Validation

### Must Pass ALL
- [ ] Success criteria are measurable (numbers, dates, documented facts)
- [ ] Multiple domain experts would agree on what constitutes a correct answer
- [ ] No subjective terms without operationalization ("best" → "highest by [metric]")
- [ ] Verifiable sources exist for the requested information

### Red Flags (Fail if Present)
- [ ] "Best", "most important", "compelling" without objective ranking criterion
- [ ] Speculative predictions ("will AI replace...")
- [ ] Pure opinion requests ("should I...")
- [ ] Quality judgments without defined rubric

### Objectivity Fix Patterns
| Subjective | Objective |
|------------|-----------|
| "the best approach" | "approaches ranked by [citation count/adoption rate/benchmark performance]" |
| "key challenges" | "challenges most frequently cited in [source type]" |
| "important factors" | "factors with highest correlation to [outcome] per [study]" |
| "compelling evidence" | "evidence from [named study type] showing [measurable effect]" |

---

## Pillar 2: BOUNDED Validation

### Must Pass ALL
- [ ] Clear point where answer is "complete"
- [ ] Finite number of valid correct answers
- [ ] Scope constraints are explicit (time, geography, entities)
- [ ] "Top N" patterns include objective ranking metric

### Red Flags (Fail if Present)
- [ ] Open-ended requests ("tell me about...")
- [ ] Ungrounded "top N" ("top 5 challenges" without ranking criterion)
- [ ] "At least N sources" without naming which ones
- [ ] "All aspects of..." or "everything about..."
- [ ] No temporal bounds for changing information

### Boundedness Fix Patterns

#### Temporal Constraints
| Unbounded | Bounded |
|-----------|---------|
| "NVIDIA financials" | "NVIDIA financials 2022-2025" |
| "AI developments" | "AI developments since January 2024" |
| "market trends" | "market trends Q1-Q4 2025" |

#### "Top N" Grounding
| Pseudo-Bounded | Truly Bounded |
|----------------|---------------|
| "top 5 approaches" | "top 5 approaches by citation count in [venue]" |
| "top challenges" | "top 3 challenges by regulatory mention frequency" |
| "leading companies" | "top 5 companies by market share per [source]" |
| "best practices" | "practices from [named frameworks: ISO, NIST, etc.]" |

#### Source Determinism
| Non-Deterministic | Deterministic |
|-------------------|---------------|
| "cite at least 3 studies" | "compare findings from [Study A] and [Study B]" |
| "peer-reviewed research" | "research from [NEJM, Nature, Science] 2023-2025" |
| "multiple sources say" | "[Source A] and [Source B] report..." |

---

## Pillar 3: CHALLENGE Validation

### Must Pass ALL
- [ ] Difficulty comes from synthesis or finding hard-to-locate information
- [ ] Requires domain expertise to answer well
- [ ] Not achievable by simple lookup or listing
- [ ] Maximum 5 deliverables (avoid kitchen-sink)

### Red Flags (Fail if Present)
- [ ] Simple factual recall ("capital of France")
- [ ] List generation without analysis
- [ ] 8+ distinct deliverables (volume, not complexity)
- [ ] Same formula applied repeatedly
- [ ] No synthesis across sources required

### Challenge Sources (Good)
- Multi-source synthesis with named sources
- Comparative analysis across entities
- Reconciling conflicting evidence
- Finding specific obscure information
- Cross-domain integration

### Kitchen-Sink Detection
Count deliverables in the prompt:
- 1-3 deliverables: ✅ Focused
- 4-5 deliverables: ⚠️ Acceptable if each is substantive
- 6-7 deliverables: ⚠️ Consider reducing
- 8+ deliverables: ❌ Too voluminous, refocus

---

## Augmentation Completeness Check

### Context Dimensions
- [ ] **Persona**: Professional role added if it aids specificity?
- [ ] **Output**: Deliverable format specified?
- [ ] **Source**: Authoritative sources named?

### Scope Dimensions
- [ ] **Temporal**: Timeframe bounded appropriately?
- [ ] **Cross-entity**: Comparison entities named if comparative?
- [ ] **Geography**: Geographic scope explicit?

### Dimension Applicability
Not all dimensions apply to all queries:

| Query Type | Usually Needed | Sometimes Needed | Rarely Needed |
|------------|----------------|------------------|---------------|
| Market analysis | Temporal, Source, Cross-entity | Geography, Persona | Output |
| Product comparison | Cross-entity, Persona | Temporal, Source | Geography |
| Academic research | Temporal, Source | Geography, Cross-entity | Persona |
| Policy analysis | Geography, Source, Temporal | Cross-entity | Persona |
| Technical how-to | Source | Temporal | Persona, Geography |

---

## Final Validation Steps

### Pre-Delivery Checklist
1. [ ] Read prompt aloud—does it sound like a clear research brief?
2. [ ] Could two different research agents return substantially similar results?
3. [ ] Is every "top N" grounded in an objective metric?
4. [ ] Are all source requirements deterministic (named, not "at least")?
5. [ ] Does the prompt fit in a single screen/scroll? (If not, likely too voluminous)

### Quality Tiers

**Excellent (Deploy)**
- All three pillars fully satisfied
- 4+ augmentation dimensions applied appropriately
- Clean, copy-ready format

**Good (Deploy with Note)**
- All three pillars satisfied
- 2-3 augmentation dimensions applied
- Minor formatting improvements possible

**Workable (Revise)**
- 1-2 pillars partially satisfied
- Clear improvement path identified
- Not ready for deployment

**Unworkable (Reject)**
- Missing essential constraints
- Fundamentally subjective
- Requires information not accessible to research agent

---

## Common Failure Modes

### 1. Pseudo-Objectivity
**Symptom:** Looks objective but experts would disagree
**Example:** "Analyze the effectiveness of remote work"
**Fix:** "Compare [Study A] and [Study B] findings on remote work's impact on [metric X, Y, Z]"

### 2. Pseudo-Boundedness
**Symptom:** Has numbers but scope is still infinite
**Example:** "List 10 AI companies making progress"
**Fix:** "List the 10 largest AI companies by 2024 funding raised per PitchBook"

### 3. Tedium Masquerading as Challenge
**Symptom:** Long prompt with many items
**Example:** "For each of 10 countries, analyze 5 metrics across 4 time periods"
**Fix:** "Compare [3 named countries] on [2-3 key metrics] from [bounded timeframe]"

### 4. Orphaned Context
**Symptom:** Persona added but not used
**Example:** "As a data scientist... what is machine learning?"
**Fix:** Either remove persona or make query use it: "As a data scientist evaluating MLOps tools, compare [A, B, C] on [deployment metrics]"
