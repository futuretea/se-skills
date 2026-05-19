---
name: rfp-response
description: This skill should be used when the user asks to "respond to an RFP", "draft an RFP response", "generate a bid response", "answer an RFI", "write a technical proposal response", or provides an RFP/RFI document that needs technical answers mapped against open source capabilities and past customization cases.
version: 0.1.0
---

# RFP Response

## Overview

Parse a customer RFP/RFI document, match each requirement against the OSS feature matrix and historical case library, and generate a categorized response draft with traceable sources and explicit gap annotations.

## Prerequisites

The following artifacts must exist before running this skill:
- `knowledge/oss-feature-matrix.md` (output of `/oss-analyze`)
- At least one case file in `knowledge/cases/` (output of `/case-import`)

If missing, prompt the user to run the corresponding Phase 0 skill first. The skill can run with only the feature matrix (without cases) in degraded mode, but case matching will be unavailable.

## Workflow

### Step 1: Parse the RFP

Read the RFP document (supports `.pdf`, `.docx`, `.md`, `.txt`) and extract a structured requirement list.

For each requirement, extract:

| Field | Description |
|-------|-------------|
| id | Auto-incrementing: R001, R002, ... |
| category | Functional / Non-functional / Integration / Security / SLA / Other |
| summary | One-sentence summary |
| detail | Full original text |
| keywords | Technical keywords extracted from the original text |

### Step 2: Match against the knowledge base

For each requirement, search as follows:

**2a. Search the feature matrix**

In `knowledge/oss-feature-matrix.md`, match by:
- Keyword matching: requirement keywords vs feature names and descriptions
- Semantic inference: the technical scenario described vs scenarios the OSS can address

**2b. Search the case library**

In `knowledge/cases/`, match by:
- Industry match: same-industry cases weighted higher
- Module match: case modules overlapping with requirement's module scope
- Pain point match: case pain descriptions similar to the requirement

**2c. Output match results per requirement**

```
R001: <summary>
  ├─ OSS coverage: <match from feature matrix> | no match
  ├─ Case coverage: <case file name> | no match
  └─ Status: COVERED_OSS | COVERED_CUSTOM | PARTIAL | GAP
```

### Step 3: Generate the response draft

Output `outputs/rfp/<customer>-rfp-draft.md`:

```markdown
---
skill: rfp-response
date: <YYYY-MM-DD>
inputs: <rfp-file>, knowledge/oss-feature-matrix.md, knowledge/cases/
status: draft
---

# RFP Response Draft — <Customer Name>

## Overview

| Metric | Count | Percentage |
|--------|-------|------------|
| Total requirements | N | 100% |
| OSS covered | N | X% |
| Case covered | N | X% |
| Partial | N | X% |
| GAP | N | X% |

## Responses

### R001 — <Requirement Summary>
**Category**: <category>
**Status**: COVERED_OSS | COVERED_CUSTOM | PARTIAL | GAP

**Response**:
<Concrete response content>

**Source**: <feature matrix row / case file path>
**Confidence**: high | medium | low

### R002 — ...
...

## GAP Registry
> Requirements with no match — needs SME confirmation

| ID | Requirement | Recommended Handling |
|----|-------------|---------------------|
| R0xx | ... | Product team to confirm roadmap |
| R0xx | ... | Evaluate customization feasibility |
```

### Step 4: Post-generation guidance

After generating the draft, guide the SE:
- Review the GAP registry first — identify which gaps can be answered
- Verify COVERED_CUSTOM items — ensure case descriptions are accurate
- Final pass for customer-specific phrasing adjustments
- Update the status to `reviewed` when done

## Key Rules

- Every response must cite its source (specific feature matrix row or case file path)
- Never fabricate capabilities. Items with no match are marked GAP
- PARTIAL items must explicitly state "what the OSS provides" and "what requires customization"
- COVERED_CUSTOM responses should reference the case's specific pitfall records to increase credibility
- Do not evaluate commercial strategy (pricing, competitive positioning) — handle technical responses only
- Avoid vague language like "can be supported, needs evaluation" — either explain how it is supported, or mark it GAP

## Quality Checklist

- [ ] RFP document successfully parsed, requirement list complete
- [ ] Every requirement cross-matched against feature matrix and case library
- [ ] Every response cites an explicit source
- [ ] All GAP items annotated, none omitted
- [ ] No vague/disclaiming language present
- [ ] Output frontmatter complete
- [ ] Overview statistics table accurate
