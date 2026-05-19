---
name: rfp-response
description: This skill should be used when the user asks to "respond to an RFP", "draft an RFP response", "generate a bid response", "answer an RFI", "write a technical proposal response", or provides an RFP/RFI document that needs technical answers mapped against open source capabilities and past customization cases. Includes Go/No-Go qualification, win theme generation, and Feature-Advantage-Benefit mapping.
version: 0.2.0
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

### Step 0: Go/No-Go Qualification

Before investing time in a full response, run a qualification check. If the SE provides enough context, score the opportunity:

| Pillar | Score | Question |
|--------|-------|----------|
| **Solution Fit** | /25 | Estimated coverage score. <50% is a red flag; <60% warns. |
| **Relationship** | /25 | Do we have a champion inside? Is this a cold RFP? |
| **Competitive Landscape** | /25 | Is the RFP wired for a competitor? Look for proprietary language. |
| **Strategic Value** | /25 | Does this align with ICP, region, and profitability goals? |

**Automatic NO-GO conditions** (hard stops — do not proceed regardless of other scores):
- Security/compliance requirements are non-negotiable and the OSS + customization cannot satisfy them
- The RFP is wired for a competitor (proprietary feature language throughout)
- The prospect refuses all pre-submission engagement
- Must-have coverage < 50%

**Red flags that suggest walking away:**
- Requirements use a competitor's proprietary feature language
- Unusually short deadline (likely a wired bid seeking backup quotes)
- Zero existing relationships and the prospect won't engage pre-submission
- Must-have gaps > 3 or coverage score < 50%

Output the qualification verdict before proceeding. If the SE chooses to proceed despite red flags, note the elevated risk. If any automatic NO-GO condition is met, recommend declining and explain why.

### Step 1: Define win themes

Before drafting any response text, define 3-5 win themes following the **Resonate → Differentiate → Substantiate** structure:

```
Theme: <What the buyer needs> → <What we deliver> → <Why they should believe us (proof)>
```

Each theme maps to at least one customer priority identified in the RFP. Win themes guide all downstream response writing — every answer should reinforce at least one theme.

### Step 2: Parse the RFP

Read the RFP document (supports `.pdf`, `.docx`, `.md`, `.txt`) and extract a structured requirement list.

For each requirement, extract:

| Field | Description |
|-------|-------------|
| id | Auto-incrementing: R001, R002, ... |
| category | Functional / Non-functional / Integration / Security / SLA / Other |
| summary | One-sentence summary |
| detail | Full original text |
| keywords | Technical keywords extracted from the original text |
| customer_priority | Inferred priority to the buyer (critical / high / medium / low) — based on emphasis in the RFP |

### Step 3: Match against the knowledge base

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

### Step 4: Generate the response draft

Output `outputs/rfp/<customer>-rfp-draft.md`:

```markdown
---
skill: rfp-response
date: <YYYY-MM-DD>
inputs: <rfp-file>, knowledge/oss-feature-matrix.md, knowledge/cases/
status: draft
---

# RFP Response Draft — <Customer Name>

## Go/No-Go Verdict
| Pillar | Score |
|--------|-------|
| Solution Fit | /25 |
| Relationship | /25 |
| Competitive Landscape | /25 |
| Strategic Value | /25 |
| **Total** | **/100** |
| **Verdict** | GO / NO-GO / GO WITH RISK |

## Win Themes
> Every response reinforces at least one of these themes

| # | Theme | Buyer Need | Our Delivery | Proof |
|---|-------|------------|-------------|-------|
| 1 | ... | ... | ... | ... |
| 2 | ... | ... | ... | ... |
| 3 | ... | ... | ... | ... |

## Coverage Overview

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
**Priority**: critical | high | medium | low
**Status**: COVERED_OSS | COVERED_CUSTOM | PARTIAL | GAP
**Win Theme**: <which theme this reinforces, or "—">

**Feature**: <What capability addresses this requirement>

**Advantage**: <What makes our approach better/different>

**Benefit**: <What tangible outcome this produces for the buyer>

**Response**:
<Concrete, buyer-language response content connecting feature → advantage → benefit>

**Source**: <feature matrix row / case file path>
**Confidence**: high | medium | low

### R002 — ...
...

## GAP Registry
> Requirements with no match — needs SME confirmation

| ID | Requirement | Priority | Recommended Handling |
|----|-------------|----------|---------------------|
| R0xx | ... | high | Product team to confirm roadmap |
| R0xx | ... | medium | Evaluate customization feasibility |

## Executive Summary
> Write last. Lift the strongest proof points and sharpest language from the completed responses.

**Decision Statement**: <1-2 sentences: the outcome + why you are the credible choice>

**Customer Situation**: <Prove you understand their world — 2-3 sentences>

**Our Approach**: <High-level solution mapped to their top 3 criteria>

**Differentiators**: <3-5 bullets with proof — pulled from win themes>

**Delivery & Risk**: <How execution will be de-risked>

**Next Step**: <Clear path forward>
```

### Step 5: Post-generation guidance

After generating the draft, guide the SE through structured review:

**Gate 1 — Compliance & Coverage:**
- Review the GAP registry first — identify which gaps can be answered
- Verify every RFP requirement has a corresponding response

**Gate 2 — Technical Accuracy:**
- Verify COVERED_CUSTOM items — ensure case descriptions and version references are accurate
- Have SMEs validate PARTIAL and GAP items (not rewrite them)

**Gate 3 — Narrative & Proof:**
- Write the Executive Summary last, lifting the strongest language from completed responses
- Run the skimmability test: if someone reads only the first sentence of each response, do they get the story?
- Verify every major claim has evidence attached (feature matrix row, case file, or SME confirmation)
- Update the status to `reviewed` when done

## Key Rules

- Run Go/No-Go before drafting. Do not invest hours in a wired bid.
- Define win themes first. Every response must reinforce at least one theme.
- Map every response through FAB: Feature → Advantage → Benefit. Do not list features without connecting them to buyer outcomes.
- Every response must cite its source (specific feature matrix row or case file path)
- Never fabricate capabilities. Items with no match are marked GAP
- PARTIAL items must explicitly state "what the OSS provides" and "what requires customization"
- COVERED_CUSTOM responses should reference the case's specific pitfall records to increase credibility
- Write the Executive Summary last, after all responses are complete
- Use the buyer's own language from the RFP — mirror their terminology
- Do not evaluate commercial strategy (pricing, competitive positioning) — handle technical responses only
- Avoid vague language like "can be supported, needs evaluation" — either explain how it is supported, or mark it GAP

## Quality Checklist

- [ ] Go/No-Go qualification completed with scores; automatic NO-GO conditions checked
- [ ] 3-5 win themes defined with Resonate → Differentiate → Substantiate structure
- [ ] RFP document successfully parsed, requirement list complete with customer priorities
- [ ] Every requirement cross-matched against feature matrix and case library
- [ ] Every response includes FAB mapping (Feature / Advantage / Benefit)
- [ ] Every response cites an explicit source and references at least one win theme
- [ ] All GAP items annotated with priority and recommended handling
- [ ] Executive Summary written after responses, using strongest proof points
- [ ] No vague/disclaiming language present
- [ ] Output frontmatter complete
- [ ] Overview statistics table accurate
