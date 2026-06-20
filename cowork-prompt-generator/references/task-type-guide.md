# Task Type Guide

Adjustments to the standard Cowork prompt structure by task category.

---

## Document Consolidation / Research Synthesis

**When:** User wants to combine multiple documents into one coherent output (e.g. case timeline from evidence files, research summary from notes)

**Key adjustments:**
- Specify the chronological or logical order Cowork should follow
- Name the sections you want in the output document explicitly
- Tell Cowork what to do when information conflicts across sources ("flag contradictions" or "use the most recent version")
- If files are large or numerous: tell it to prioritise recency or relevance

**Example constraint:** "If two documents give conflicting dates, note both and flag for review."

---

## Folder Organisation / Triage

**When:** User wants files sorted, renamed, or moved into a logical structure

**Key adjustments:**
- Describe the target folder structure explicitly (e.g. "organise by year, then by document type")
- Specify naming convention for renamed files (e.g. "YYYY-MM-DD_Description.docx")
- Always include: "Do not delete any files — move only, or create copies"
- Ask for a manifest/log file listing what was moved and where

**Example output addition:** "Also produce a manifest.txt file listing every file moved, its original location, and its new location."

---

## Professional Document Production (Word / Excel / PowerPoint)

**When:** User wants a formatted, shareable output document

**Key adjustments:**
- Specify the exact file format (.docx, .xlsx, .pptx)
- List any required sections, headings, or slide titles
- For Excel: describe what each sheet should contain, and whether formulas are needed
- For Word: mention if headers, page numbers, or a table of contents are needed

**Example constraint:** "Use clear heading styles (Heading 1, Heading 2). Include a table of contents at the start."

---

## Legal / Tribunal Document Work

**When:** User is preparing or analysing legal documents (Ciarán's primary use case)

**Key adjustments:**
- Be explicit about which party's perspective to adopt (or remain neutral)
- Specify legal formatting requirements if any (e.g. numbered paragraphs, dated entries)
- Always include: "Do not add legal conclusions — flag ambiguities for human review"
- Include a "Gaps and questions" section in the output for anything Cowork couldn't determine

**Example constraint:** "Flag any dates, names, or facts that appear inconsistent across documents. Do not resolve the inconsistency — note it."

---

## Draft Writing from Notes

**When:** User has scattered notes and wants a polished draft (article, report, brief)

**Key adjustments:**
- Tell Cowork the target audience and tone (e.g. "professional legal audience", "Substack readers, sardonic tone")
- Specify approximate target length
- Tell it whether to synthesise freely or stay close to the source material
- Indicate if certain source documents should be weighted more heavily

**Example goal:** "Draft a 1,200-word Substack article in my established sardonic voice, synthesising the notes in the folder. Do not add information not present in the notes."

---

## Bulk Processing / Template Filling

**When:** User has many files of the same type to process uniformly

**Key adjustments:**
- Give one worked example of input → output if possible
- Specify how errors or edge cases should be handled ("skip and log" vs "stop and flag")
- Request a processing log showing what was done to each file

**Example constraint:** "If a file cannot be processed, skip it and add it to an errors.log file with a brief reason."
