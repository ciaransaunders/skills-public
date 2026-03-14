# Domain-Specific Augmentation Patterns

Patterns extracted from DRACO benchmark's 10 domains with highest-performing prompt structures.

## Finance (20% of benchmark)

**Key augmentations:**
- Temporal: Always specify fiscal years or quarters
- Source: Name SEC filings, 10-K, proxy statements, earnings calls
- Cross-entity: Add peer comparisons (competitors, sector benchmarks)
- Metrics: Specify exact financial metrics (CAGR, EBITDA, margins)

**Template:**
```
From [YEAR-YEAR], analyze [COMPANY/SECTOR] focusing on:
(1) [Specific metric] with year-over-year trends
(2) [Comparative metric] vs [named peers]
(3) [Forward-looking element] based on [specific filings]
Source: [10-K filings, proxy statements, earnings transcripts, named data providers]
```

**Example:**
```
From 2022-2025, analyze NVIDIA's data center revenue segment:
(1) Quarterly revenue growth rates and gross margin trends
(2) Market share vs AMD and Intel in AI accelerators per Mercury Research
(3) Capex guidance vs actual spend from earnings call transcripts
Source: NVIDIA 10-K filings, quarterly earnings transcripts, Mercury Research reports
```

---

## Technology (12% of benchmark)

**Key augmentations:**
- Temporal: "Since [year]" or specific version releases
- Source: Peer-reviewed papers, benchmark results, official documentation
- Methods: Name specific technical approaches, not "top approaches"

**Template:**
```
Since [YEAR], describe [TECHNOLOGY AREA] addressing:
(1) [Named method 1], [method 2], [method 3] with technical details
(2) Performance metrics from [named benchmarks]
(3) [Regulatory/ethical dimension] citing [specific frameworks]
Include: peer-reviewed papers, benchmark evaluation results, [named standards body] guidance
```

**Example:**
```
Since 2022, describe deepfake detection research addressing:
(1) Cross-dataset generalization approaches, transformer architectures, multimodal audio-visual analysis
(2) Performance differences between FaceForensics++ benchmark and real-world deployment
(3) Regulatory frameworks from EU AI Act, US NIST guidelines
Include: peer-reviewed papers from major CV conferences, benchmark leaderboard results, enacted policy documents
```

---

## Academic (16% of benchmark)

**Key augmentations:**
- Cross-entity: Compare scholarly perspectives across regions/schools
- Source: Name specific journals, methodologies, primary sources
- Temporal: Distinguish historical analysis from recent scholarship

**Template:**
```
Examine [HISTORICAL/ACADEMIC TOPIC] from [TIMEFRAME]. Compare how scholars from [REGION A], [REGION B], [REGION C] interpret [specific evidence type]. Analyze how [contemporary factor] influences historical narratives about [specific thesis].
```

**Example:**
```
Examine Indian Ocean trade networks from 1000-1500 CE. Compare how scholars from East Africa, the Arabian Peninsula, South Asia, and Southeast Asia interpret archaeological evidence, linguistic diffusion patterns, and manuscript sources differently. Analyze how contemporary geopolitical tensions influence historical narratives about maritime hegemony.
```

---

## Medicine (9% of benchmark)

**Key augmentations:**
- Geography: Specify healthcare systems, regulatory jurisdictions
- Source: WHO standards, FDA/EMA guidance, named clinical registries
- Metrics: Sensitivity/specificity, clinical trial outcomes

**Template:**
```
As [HEALTHCARE ROLE] for [REGION/CONTEXT], compare [SOLUTIONS/PRODUCTS] from [MANUFACTURERS] on:
(1) [Clinical performance metric] per [regulatory standard]
(2) [Operational metric] in [specific settings]
(3) [Cost/access metric] per delivered unit
Source: [Regulatory body] prequalification standards, [named registries], manufacturer filings
```

**Example:**
```
As procurement lead for pharmaceutical cold chain in West Africa, compare temperature-controlled transport from Thermo King, Carrier Transicold, and off-grid alternatives on:
(1) Reliability during 12+ hour journeys without electricity
(2) Real-time temperature monitoring capabilities (cellular/satellite)
(3) Total cost per vaccine dose maintaining WHO Prequalification standards
Source: WHO PQS specifications, manufacturer technical documentation, field deployment reports
```

---

## Law (9% of benchmark)

**Key augmentations:**
- Source: Name specific statutes, regulations, listing standards
- Scope: Bound to specific jurisdiction, entity type
- Output: Define/list format expectations explicitly

**Template:**
```
Define [LEGAL TERM/REQUIREMENT] under [SPECIFIC REGULATORY FRAMEWORK].
(1) List eligibility criteria (who qualifies)
(2) List disqualification criteria (who cannot serve/apply)
(3) Which [entity types] are required to [comply]
Source: [Regulatory body] listing rules, [specific statute sections]
```

**Example:**
```
Define an independent director under NASDAQ listing standards.
(1) List eligibility criteria (who qualifies)
(2) List disqualification criteria (who cannot serve)
(3) Which company types are required to have independent directors
Source: NASDAQ Rule 5605, SEC Rule 10A-3, relevant no-action letters
```

---

## Shopping/Product Comparison (10% of benchmark)

**Key augmentations:**
- Persona: Professional use case context
- Cross-entity: Name specific products/models to compare
- Metrics: Objective, measurable specifications
- Output: Total cost of ownership over timeframe

**Template:**
```
I'm a [PROFESSIONAL ROLE] transitioning/upgrading for [USE CASE] in [LOCATION]. Compare [PRODUCT A], [PRODUCT B], [PRODUCT C] for:
(1) [Technical spec 1] with quantified measurements
(2) [Workflow metric] in [specific software/context]
(3) [Ecosystem cost] for [specific components]
Include total system investment over [N years] including [depreciation, subscriptions, maintenance].
```

**Example:**
```
I'm a professional photographer transitioning to medium format for commercial fashion work in New York. Compare Fujifilm GFX100 II, Hasselblad X2D 100C, and Phase One XF IQ4 150MP for:
(1) Studio strobe sync reliability and tethered shooting performance with Capture One Pro
(2) Color science accuracy for skin tones across diverse ethnicities
(3) Lens ecosystem costs for 35mm, 80mm, and 110mm equivalents
Include total system investment over 3 years including body depreciation, software subscriptions, and local rental availability.
```

---

## General Knowledge (10% of benchmark)

**Key augmentations:**
- Geography: Multi-region case studies
- Cross-entity: Comparative analysis across named examples
- Scope: Specific phenomena to analyze

**Template:**
```
Document [PHENOMENON] comparing case studies from [REGION A: specific example], [REGION B: specific example], [REGION C: specific example]. Analyze:
(1) [Trend A] with quantified data
(2) [Impact B] on [specific stakeholders]
(3) [Dimension C] arguments
Include [specific conflict or tension to address].
```

**Example:**
```
Document industrial agriculture mega-farm expansion and resistance, comparing case studies from Ukraine's grain operations, Brazilian cerrado soy plantations, Saudi Arabia's desert farming investments in Arizona, and Chinese pork production facilities. Analyze:
(1) Land consolidation trends with hectare/acre figures
(2) Water resource depletion impact on rural communities
(3) Food security vs environmental sustainability arguments
Include indigenous land rights conflicts and displacement data.
```

---

## UX Design (6% of benchmark)

**Key augmentations:**
- Source: Name specific products, studies, research teams
- Metrics: User research metrics (acceptance rates, latency thresholds)
- Cross-entity: Compare across experience levels

**Template:**
```
I'm designing [FEATURE TYPE] for [USER CONTEXT]. Compare findings from [PRODUCT A], [PRODUCT B], [PRODUCT C] across [USER SEGMENT 1] vs [USER SEGMENT 2]. What does research reveal about:
(1) [Metric 1] thresholds
(2) [Metric 2] correlated with [behavior]
(3) [Metric 3] impact on [outcome]
Synthesize evidence from [named studies/teams] to inform [specific design decision].
```

**Example:**
```
I'm designing AI code completion for enterprise teams. Compare findings from GitHub Copilot's inline suggestions, Tabnine's multi-line predictions, and Amazon CodeWhisperer's comment-to-code across developers with 2-5 years vs 10+ years experience. What does research reveal about:
(1) Optimal suggestion latency thresholds (milliseconds)
(2) Acceptance rates correlated with interruption timing during debugging vs new feature development
(3) Explanation availability impact on developer trust calibration
Synthesize evidence from Microsoft's productivity studies, academic interruption cost research, and JetBrains' AI assistant metrics.
```

---

## Personalized Assistant (6% of benchmark)

**Key augmentations:**
- Persona: Detailed personal context (age, income, location, dependents)
- Temporal: Planning horizon with specific end date
- Geography: Tax jurisdiction, local regulations

**Template:**
```
I'm a [AGE]-year-old [PROFESSION] in [LOCATION] earning [INCOME] with [PERSONAL CONTEXT]. I need to [GOAL] that accommodates [CONSTRAINT]. Compare:
(1) [Option A] vs [Option B] given [jurisdiction] tax implications at my income level
(2) [Strategy A] vs [Strategy B] over [timeframe] before [milestone]
(3) Optimal allocation between [accounts] considering [liquidity need]
Which strategy maximizes [objective] by [target year]?
```

**Example:**
```
I'm a 42-year-old freelance graphic designer in Toronto earning CAD 95,000 annually with irregular monthly income, supporting two children aged 8 and 11. I need tax-efficient investment strategy accommodating variable cash flow while maximizing RESP contributions and building RRSP retirement savings. Compare:
(1) Spousal RRSP vs individual RRSP given Ontario's marginal tax rates at my income
(2) Front-loading RESP contributions vs spreading them evenly over next 7 years before eldest starts university
(3) Optimal monthly allocation between TFSA, RRSP, and RESP considering 6-month emergency fund requirement
Which strategy maximizes after-tax wealth accumulation by 2032?
```

---

## Needle in a Haystack (6% of benchmark)

**Key augmentations:**
- Specificity: Exact identifying details (year, features, context)
- Source: Request contemporaneous sources
- Output: Name + verification source

**Template:**
```
In [YEAR], [ENTITY] [ACTION/CREATED] featuring [SPECIFIC DETAIL 1] and [SPECIFIC DETAIL 2]. Find:
(1) The name of [target] who [role]
(2) Locate a contemporaneous source ([year] or earlier) describing [specific aspect]
```

**Example:**
```
In 2008, Longwood Gardens opened "Nature's Castles: The Treehouse Reimagined" featuring three treehouse structures. Find:
(1) The name of the architectural firm or designer who created these treehouses
(2) Locate a contemporaneous source (2008 or earlier) describing the design concept and construction process
```
