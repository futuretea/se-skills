---
name: discovery-prep
description: This skill should be used when the user asks to "prepare for a discovery call", "create a call plan", "plan a technical discovery meeting", "prepare customer questions", "build a discovery guide", "run a disco-stration", or needs to generate a structured interview guide for a first or follow-up customer technical discovery session. Incorporates MEDDPICC qualification, ambition-oriented questioning, and discovery-demo blending techniques.
version: 0.2.0
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

### Step 1b: Apply MEDDPICC qualification

Score the opportunity against the MEDDPICC framework to surface blind spots before the call:

| Element | Status | What to probe in the call |
|---------|--------|---------------------------|
| **M**etrics (value levers) | Known / Unknown | What KPIs does the buyer care about? |
| **E**conomic Buyer | Known / Unknown | Who signs? What is their priority? |
| **D**ecision Criteria | Known / Unknown | How will they evaluate solutions? |
| **D**ecision Process | Known / Unknown | Steps from eval to purchase? |
| **I**mplicit Pain | Known / Unknown | What is the cost of doing nothing? |
| **C**hampion | Identified / Missing | Who is our internal advocate? |
| **C**ompetition | Known / Unknown | Who else is in the eval? |

Elements marked "Unknown" or "Missing" become priority probe targets in the question path.

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

### Phase 2: Pain Points & Ambition (15 min)

Mix pain-oriented and ambition-oriented questions:

| Type | Example | Purpose |
|------|---------|---------|
| Pain | "What slows your team down most right now?" | Surface explicit friction |
| Pain | "If nothing changed, what would that cost you in 12 months?" | Quantify cost of inaction |
| Ambition | "What would success feel like to your team?" | Reveal emotional drivers |
| Ambition | "If our platform could fix one thing tomorrow, what would it be?" | Prioritize value delivery |
| Ambition | "Where does your leadership want this team to be in 2 years?" | Surface strategic context |

1. <Question> — Purpose: <why this question, which capability it relates to>
2. ...

### Phase 3: Solution Probing (10 min)

**Gap Selling questions** — quantify the cost of inaction before proposing solutions:
- "You mentioned X takes 3 hours a day. What would recovering those 3 hours mean for your team's output?"
- "If this problem stays unsolved for another 6 months, what is the business cost?"
- "What is stopping you from solving this today?"

1. <Question> — Purpose: <why this question>
2. ...

**Landmine questions** — surface competitor weaknesses without criticizing them directly:
- "What is your contingency plan if implementation drags past the expected timeline?"
- "How are you handling [known competitor gap] today?"
- "Who on your team will own the integration work post-deployment?"

> Add specific landmine questions tailored to the competitive signals identified in Step 1.

### Disco-Stration Option
> When discovery and demo are merged into a single fluid session

Instead of separate Q&A and demo blocks, intersperse demonstration moments within discovery:
- Show a relevant capability, then ask: *"Does this map to how you operate?"*
- Use the "Reverse Demo": ask the customer to walk through *their* current workflow first, then reveal the corresponding capability
- Shift between question and demonstration based on buyer reactions, not a rigid script

If the SE plans a Disco-Stration, note it here and reduce Phase 3 (Solution Probing) time accordingly.

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

- **Company Overview**: <1-2 sentences summarizing who they are>
- **Team Goals**: <What outcome are they driving toward>
- **Current Workflow**: <How do they operate today, key friction points>
- **Pain Points** (with direct quotes if possible):
  - ...
  - ...
- **Decision Makers** (roles identified):
  - ...
  - ...
- **Competition**: <Who else is in the eval>
- **MEDDPICC Scorecard** (updated):
  | Element | Post-call status |
  |---------|-----------------|
  | M | ... |
  | E | ... |
  | D (Criteria) | ... |
  | D (Process) | ... |
  | I | ... |
  | C (Champion) | ... |
  | C (Competition) | ... |
- **Validated Hypotheses**:
  - ...
- **Newly Discovered Pain Points**:
  - ...
- **Action Items**:
  - [ ] ...
- **Next Call Objective**: <one sentence>
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
- [ ] MEDDPICC elements assessed, unknowns flagged as probe targets
- [ ] Core hypotheses have concrete verification methods (not "ask them")
- [ ] Question path includes both pain-oriented and ambition-oriented questions
- [ ] Question path follows the three-phase progression
- [ ] Ammo Belt references at least 2 relevant cases or OSS capabilities
- [ ] Avoid items are specific, not generic
- [ ] Post-Call Notes template includes MEDDPICC scorecard update
- [ ] Output frontmatter complete
