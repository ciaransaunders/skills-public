# Example Cowork Prompts

Worked examples across common task types.

---

## Example 1: Legal Case Timeline (Tribunal Evidence)

**User said:** "I want Cowork to go through all my tribunal evidence and build me a chronological timeline."

**Generated Cowork Prompt:**

```
## Goal
Produce a chronological timeline of key events in my employment tribunal case, drawn from all documents in the workspace folder.

## Workspace
~/Documents/Tribunal/Evidence/

## Inputs
All .pdf, .docx, and .msg files in the Evidence folder and its subfolders.

## What to Produce
Save as: Case_Timeline_Draft.docx in ~/Documents/Tribunal/

Structure:
- Chronological table with columns: Date | Event Description | Source Document | Page/Reference | Notes
- Summary section at the end listing any date gaps or periods with no documentary evidence
- "Flags" section listing any inconsistencies or conflicting information found

## Constraints
- Do not modify any source files
- Do not draw legal conclusions — describe events factually as documented
- Where a date is approximate or unclear, note it as "approx." and state the basis
- Flag any document that appears to be missing pages or is truncated

## Notes
This is for EA-2025-000680-RS, an employment tribunal claim. Documents include witness statements, emails, HR correspondence, and occupational health reports. The respondent is Peloton Interactive UK.
```

---

## Example 2: Substack Article from Research Notes

**User said:** "I've got a folder of notes on Lewis Silkin's tactical behaviour in my case — I want Cowork to turn them into a Substack draft."

**Generated Cowork Prompt:**

```
## Goal
Draft a Substack article (~1,500 words) in my established sardonic, analytical voice, synthesising my research notes on Lewis Silkin's litigation tactics.

## Workspace
~/Documents/Substack/LewisSlilkin_Research/

## Inputs
All .txt and .md files in the folder. Prioritise files named "tactics_notes*" and "observations*".

## What to Produce
Save as: LewisSlilkin_Draft.md in the same folder.

Structure:
- Opening hook (1-2 paragraphs, visceral/sardonic, sets up the argument)
- Main body: 3-4 tactical patterns, each with supporting examples from the notes
- Closing paragraph: broader implications for LIPs facing repeat-player law firms
- Suggested subheadings throughout

## Constraints
- Do not add any facts or claims not present in the source notes
- Do not use emojis or corporate language
- Maintain a sardonic, legally-informed first-person voice — not neutral, not academic
- Flag any section where the notes are thin and more research may be needed

## Notes
This is for my Substack which analyses employment law, AI, and legal tech from a litigant-in-person perspective. The tone is intelligent, irreverent, and rigorous. Think Private Eye meets Employment Law Weekly.
```

---

## Example 3: Folder Organisation

**User said:** "My Downloads folder is chaos. Can Cowork sort it out?"

**Generated Cowork Prompt:**

```
## Goal
Organise the contents of my Downloads folder into a clean, logical folder structure, and produce a manifest of everything that was moved.

## Workspace
~/Downloads/

## Inputs
All files currently in ~/Downloads/ (top level only — do not recurse into existing subfolders).

## What to Produce
1. Reorganised folder structure within ~/Downloads/ using these categories:
   - Documents/ (Word, PDF, text files)
   - Spreadsheets/ (Excel, CSV)
   - Images/ (jpg, png, gif, webp)
   - Archives/ (zip, tar, gz)
   - Installers/ (dmg, pkg, exe)
   - Misc/ (anything that doesn't fit the above)
2. A file: ~/Downloads/Organisation_Manifest.txt listing every file moved, its original location, and its new location, with the date processed.

## Constraints
- Do not delete any files — move only
- Do not rename files — preserve original filenames
- If a category folder already exists, move into it rather than creating a duplicate
- If a file type is ambiguous, place in Misc/ and note it in the manifest

## Notes
There are likely 200+ files. Some may have unusual extensions. When in doubt, use Misc/.
```

---

## Example 4: Tribunal Harness Architecture Doc

**User said:** "I want Cowork to take my scattered Tribunal Harness notes and build a clean architecture document."

**Generated Cowork Prompt:**

```
## Goal
Synthesise all Tribunal Harness project notes into a single, clean architecture document suitable for sharing with a developer.

## Workspace
~/Documents/TribunalHarness/Notes/

## Inputs
All .md, .txt, and .docx files in the Notes folder.

## What to Produce
Save as: TribunalHarness_Architecture_v1.docx in ~/Documents/TribunalHarness/

Structure:
1. Project Overview (2-3 paragraphs: what the platform does, who it's for, core value proposition)
2. User Personas (brief descriptions of the target user types)
3. Core Features (grouped by functional area)
4. Technical Architecture (data flows, key components, integrations noted in the notes)
5. Employment Rights Act 2025 Considerations (any notes referencing ERA 2025 changes)
6. Open Questions / Unresolved Design Decisions (anything flagged as uncertain in the notes)

## Constraints
- Do not invent features or architecture not present in the notes
- Flag sections where the notes are incomplete or contradictory
- Use clear heading styles suitable for a technical document
- Plain language — avoid jargon unless it appears in the notes

## Notes
Tribunal Harness is a legal intelligence platform for UK employment tribunal litigants-in-person. It incorporates Employment Rights Act 2025 changes. Notes may be fragmentary and non-linear.
```
