---
name: case-import
description: This skill should be used when the user asks to "record a case", "import a case study", "document a past project", "save a customer story", "capture lessons learned", or needs to structure a project narrative into a standardized case format for the knowledge base. Also triggered after project delivery, deal closure, or win/loss review to preserve tribal knowledge.
version: 0.2.0
---

# Case Import

## Overview

Convert an SE's natural-language project narrative into a structured case file stored in `knowledge/cases/`, making it searchable and matchable by downstream skills like `/rfp-response` and `/discovery-prep`.

## When to Use

Use this skill after completing a customer project, when onboarding past projects from memory, or whenever a new customization pattern emerges. Run for every significant customer engagement to build the case library.

## Workflow

### Step 1: Collect the initial narrative

Prompt the SE to describe the project in natural language:

```
Describe the project:
- Who was the customer? (industry, scale)
- When did this engagement happen? (quarter/year)
- What problem did they have? (specific pain points)
- What OSS version did you start from?
- What customizations were made? (modules, features, effort)
- What went wrong during implementation? (pitfalls)
- What was the deal outcome? (won / lost / ongoing) — if lost, who won and why?
- What deal stage was this? (discovery / evaluation / poc / negotiation / closed)
- Any quantifiable results?
```

### Step 2: Identify information gaps

Map the provided information against the required and optional fields:

| Required | Optional |
|----------|----------|
| customer | scale |
| industry | effort |
| engagement_date | key_metric |
| oss_version | team_size |
| modules | contact |
| pain_points | tech_stack |
| solution_summary | lessons_learned |
| deal_outcome | competitor_displaced |
| deal_stage | competitor_won |
| | project_status |

**Deal outcome values**: `won` | `lost` | `ongoing`
**Deal stage values**: `discovery` | `evaluation` | `poc` | `negotiation` | `closed`

### Step 3: Fill gaps (max 3 rounds)

Ask targeted questions for each missing required field. Reference context the SE has already provided — do not ask questions whose answers were already given.

### Step 4: Generate the case file

Output `knowledge/cases/<customer-slug>-<project-slug>.md`:

```markdown
---
skill: case-import
date: <YYYY-MM-DD>
inputs: <SE narrative>
status: draft
customer: <customer name>
industry: <industry>
engagement_date: <YYYY-QN>
oss_version: <vX.Y>
modules:
  - <module-1>
  - <module-2>
effort: <X person-weeks>
deal_outcome: won | lost | ongoing
deal_stage: discovery | evaluation | poc | negotiation | closed
competitor: <competitor displaced, or competitor who won>
project_status: live | POC | delivered
---

## Customer Background
<1-2 sentences on the customer's industry, scale, and technical state>

## Pain Points
<Specific problems encountered. The more concrete, the better.>

## Solution
<Which OSS modules were extended, and how:

1. Customization 1 — rationale and approach
2. Customization 2 — rationale and approach
>

## Pitfalls
<Problems encountered during implementation and their resolutions:
- Pit 1: symptom → root cause → fix
- Pit 2: symptom → root cause → fix
>

## Results
<Quantifiable outcomes or customer feedback>

## Deal Analysis
> Only applicable for won or lost deals

**Why we won** (or **Why we lost**):
<2-3 specific reasons. For losses: what would have changed the outcome? For wins: what was the decisive factor?>

**Competitive Context**:
<Who else was evaluated? How did we position? What worked/didn't work against each competitor?>
```

### Step 5: Confirm and index

- Echo the file path back to the SE for confirmation
- Remind the SE that the case file will be automatically matched against future RFP requirements

## Notes

- Case file names use kebab-case: `<customer>-<project>.md`
- Customer-sensitive details should be abstracted at the SE's discretion (e.g., "a retail enterprise", "a bank")
- Required fields that the SE cannot provide should be annotated `<!-- TODO -->` — never fabricate
- The `modules` field should align with module names in `oss-feature-matrix.md` for accurate downstream matching

## Quality Checklist

- [ ] All required fields filled or annotated as TODO
- [ ] Artifact-level frontmatter present: `skill`, `date`, `inputs`, `status`
- [ ] Engagement date, deal outcome, deal stage, competitor, and project_status fields present
- [ ] Pain points are specific and tangible (not generic phrases like "wanted to improve efficiency")
- [ ] Pitfall records include the symptom → root cause → fix chain
- [ ] Deal Analysis section filled for won/lost deals
- [ ] Module names aligned with the feature matrix
- [ ] File name follows kebab-case convention
