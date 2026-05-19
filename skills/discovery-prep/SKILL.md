---
name: discovery-prep
description: This skill should be used when the user asks to "prepare for a discovery call", "create a call plan", "plan a technical discovery meeting", "prepare customer questions", "build a discovery guide", or needs to generate a structured interview guide for a first or follow-up customer technical discovery session.
version: 0.1.0
---

# Discovery Prep

## Overview

Generate a structured call plan for a customer technical discovery meeting based on available customer context, industry patterns, and the knowledge base. The plan ensures the SE enters the call with clear objectives, testable hypotheses, and relevant reference material.

## Prerequisites

At least one of the following should be provided. If none is available, prompt the SE to supply basic context before proceeding:
- Customer name or industry
- Known background (CRM records, Slack discussions, email threads)
- Meeting objective (initial contact, solution presentation, POC kickoff)

## Workflow

### Step 1: Consolidate known information

Assemble a customer profile from SE input and available assets:

| Dimension | Source |
|-----------|--------|
| Industry & scale | SE input |
| Known pain points / interests | SE input |
| Tech stack (known or inferred) | SE input |
| Decision chain (known roles) | SE input |
| Competitive signals | SE input |
| Same-industry cases | `knowledge/cases/` — match by industry |
| Relevant OSS capabilities | `knowledge/oss-feature-matrix.md` — modules likely relevant |

### Step 2: Generate hypotheses

Formulate testable hypotheses about the customer's situation. Each hypothesis must include a verification method and the implication if confirmed.

### Step 3: Build the call plan

Output `outputs/discovery/<customer>-call-plan.md`:

```markdown
---
skill: discovery-prep
date: <YYYY-MM-DD>
inputs: <summary of SE input>
status: draft
---

# Call Plan — <Customer Name>

## Customer Profile
- **Industry**: ...
- **Known Background**: ...
- **Tech Stack (inferred)**: ...
- **Decision Roles**: ...

## Meeting Objective
<One sentence: what must be learned by the end of this call>

## Core Hypotheses (to validate)
| # | Hypothesis | Verification Method | If True, Means |
|---|-----------|--------------------|-----------------|
| 1 | ... | What to ask | ... |
| 2 | ... | What to observe | ... |

## Question Path

### Phase 1: Current State (10 min)
1. <Question> — Purpose: <why this question>
2. ...

### Phase 2: Pain Points (15 min)
1. <Question> — Purpose: <why this question, which capability it relates to>
2. ...

### Phase 3: Solution Probing (10 min)
1. <Question> — Purpose: <why this question>
2. ...

## Ammo Belt
> Relevant cases and capabilities to deploy during the conversation

| If customer mentions... | Reference... | Source |
|------------------------|--------------|--------|
| <Scenario A> | <Our approach / case> | case: ... |
| <Scenario B> | <OSS capability> | feature matrix: ... |

## Avoid
- <Specific capability not to promise>
- <Sensitive topic>
- <Angle to avoid in competitive comparison>

## Post-Call Notes
> Fill in after the call
- Validated hypotheses:
- Newly discovered pain points:
- Next steps:
```

### Step 4: Post-generation guidance

- Remind the SE to fill in the Post-Call Notes section after the meeting
- If the call reveals significant new learnings, suggest running `/case-import` to preserve them
- The completed call plan can serve as input to subsequent `/discovery-prep` runs for the same customer

## Notes

- The question path follows a "current state → pain points → solution probing" progression. Do not lead with the product.
- The Ammo Belt must include at least 2 same-industry case references. If the case library lacks them, annotate `<!-- GAP: suggest adding <industry> cases -->`
- Avoid items must be specific — no generic advice like "avoid technical jargon"
- Call plans are living documents. SEs should update them as the engagement progresses

## Quality Checklist

- [ ] Customer profile includes at minimum industry and known background
- [ ] Core hypotheses have concrete verification methods (not "ask them")
- [ ] Question path follows the three-phase progression
- [ ] Ammo Belt references at least 2 relevant cases or OSS capabilities
- [ ] Avoid items are specific, not generic
- [ ] Output frontmatter complete
