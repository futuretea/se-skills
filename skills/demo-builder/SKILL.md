---
name: demo-builder
description: This skill should be used when the user asks to "build a demo script", "create a demo", "prepare a product demonstration", "generate a demo narrative", "plan a customer demo", or needs to structure a customer-facing technical demonstration with step-by-step instructions, fallback plans, and environment checklists.
version: 0.1.0
---

# Demo Builder

## Overview

Generate a structured demonstration script from customer scenario and feature requirements. The output includes scene-by-scene narrative, operation steps, expected results, fallback plans, and environment/data preparation checklists.

## Prerequisites

At minimum, the customer's industry and a list of features to demonstrate are required. The following optional inputs enhance the result:
- `outputs/discovery/<customer>-call-plan.md` — for personalized scenario design
- `knowledge/oss-feature-matrix.md` — for verifying feature availability in the OSS version
- `knowledge/cases/` — for real-world success stories to weave in

## Workflow

### Step 1: Collect demo requirements

Extract from SE input:

| Field | Description | Required |
|-------|-------------|----------|
| customer | Customer name | Yes |
| industry | Industry | Yes |
| scenario | Demo scenario description | Yes |
| features | Features to showcase | Yes |
| duration | Total time in minutes | No, default 30 |
| audience | Technical / Business / Executive | No |
| pain_refs | Known pain points (can read from call-plan) | No |

### Step 2: Design the narrative arc

Structure the demo in three acts:

```
[Pain Resonance 20%] → [Solution Walkthrough 60%] → [Wow Moment 20%]
```

- **Pain Resonance**: Restate known pain points in the customer's industry language. Reference similar pain descriptions from same-industry cases.
- **Solution Walkthrough**: Demonstrate each feature, always connecting back to a previously stated pain point. The frame is "here is how we solve the problem you just described."
- **Wow Moment**: One unexpected reveal — a quantified result from a past case, a capability competitors lack, or an operation that makes the audience lean in.

### Step 3: Generate the demo script

Output `outputs/demo/<customer>-demo-script.md`:

```markdown
---
skill: demo-builder
date: <YYYY-MM-DD>
inputs: <SE input>, <referenced call-plan/case/feature-matrix>
status: draft
---

# Demo Script — <Customer Name>

## Basic Info
- **Duration**: N minutes
- **Audience**: Technical / Business / Executive
- **Goal**: <One sentence: what the audience should believe after the demo>

## Data Preparation Checklist

> Confirm all data is ready before the demo

| # | Data Item | Description | Done |
|---|-----------|-------------|------|
| 1 | <Industry> dataset | <Specific content> | ☐ |
| 2 | User/role configuration | <Roles, permissions> | ☐ |
| 3 | ... | ... | ☐ |

## Environment Checklist

| # | Check | Verification | Done |
|---|-------|-------------|------|
| 1 | <Service/module> running | curl / page check | ☐ |
| 2 | Network connectivity | ... | ☐ |
| 3 | Correct version | git tag / version endpoint | ☐ |

## Demo Flow

### Scene 1: <Scene Name> (Pain Resonance, ~N min)

**Narrative**:
<What the SE says, in conversational language, with pause markers>

**Actions**:
1. Open <page/terminal> → enter <command/parameters>
2. Click <button/menu> → display <content>
3. ...

**Expected Result**:
<What the screen should show>

**Fallback**:
If <step X> fails:
- Plan A: Switch to pre-recorded GIF (path: ...)
- Plan B: Verbal explanation + screenshot (path: ...)
- Plan C: Skip, transition phrase: "..."

### Scene 2: <Scene Name> (Solution Walkthrough, ~N min)
...

### Scene N: <Scene Name> (Wow Moment, ~N min)
...

## Transition Quick-Reference

| From | To | Transition Line |
|------|----|-----------------|
| Scene 1 → 2 | "We just saw the problem — here is how we solve it..." | ... |
| Scene 2 → 3 | ... | ... |

## Q&A Ammo
> Common post-demo questions with prepared responses

| Customer may ask | Response direction | Reference |
|-----------------|-------------------|-----------|
| Deployment complexity? | ... | feature matrix: deployment |
| Integration with existing system X? | ... | case: <case> |
| Performance benchmarks? | ... | ... |
```

### Step 4: Post-generation guidance

- SE should check off the Data Preparation and Environment checklists the day before the demo
- Fallback screenshots/GIFs should be recorded before the live demo
- If the demo reveals new customer requirements, suggest running `/case-import` or updating the discovery call plan

## Design Rules

- For a 30-minute slot, live operations should not exceed 20 minutes — reserve 10 minutes for interaction and contingencies
- Fallback plans must be executable (concrete file paths, reproducible steps), not "improvise"
- Data preparation items must specify concrete content, not "prepare some test data"
- Narrative language must be conversational with pause markers (`<pause>`), not read-from-script stiff
- For executive audiences: reduce action steps, emphasize outcome comparisons. For technical audiences: add architecture and data flow explanations
- Use industry-realistic data models (even with only 20 records). Never use placeholder values like `test_user_1`

## Quality Checklist

- [ ] Narrative arc follows the three-act structure, each scene has a clear purpose
- [ ] Each scene includes actions, expected results, and fallback plan
- [ ] Data preparation checklist items are specific and executable
- [ ] Environment checklist covers critical dependencies
- [ ] Transition lines read naturally, not forced
- [ ] Q&A Ammo anticipates at least 3 common questions
- [ ] Total duration is reasonable (live operations at 60-70% of total)
- [ ] Output frontmatter complete
