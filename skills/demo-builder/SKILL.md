---
name: demo-builder
description: This skill should be used when the user asks to "build a demo script", "create a demo", "prepare a product demonstration", "generate a demo narrative", "plan a customer demo", or needs to structure a customer-facing technical demonstration with step-by-step instructions, fallback plans, and environment checklists. Incorporates Tell-Show-Tell rhythm, persona-driven vignettes, and demo hierarchy (Standard → Custom → POC → POV).
version: 0.2.0
---

# Demo Builder

## Overview

Generate a structured demonstration script from customer scenario and feature requirements. The output includes scene-by-scene narrative, operation steps, expected results, fallback plans, and environment/data preparation checklists.

## Prerequisites

At minimum, the customer's industry and a list of features to demonstrate are required. If either is missing, prompt the SE to provide them before proceeding.

The following optional inputs enhance the result:
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

### Step 2: Determine demo level

Classify the demo into one of four levels. Downstream structure adapts to the level:

| Level | Effort | When to Use | Customization |
|-------|--------|-------------|---------------|
| **Standard Demo** | Low | First call, broad interest | <10%, OOTB gallery, education + vision setting |
| **Custom Demo** | Medium | Validated pain, specific use case | >10%, requires discovery write-up and buyer sign-off |
| **Technical POC** | High | Technical validation needed | SSO, integrations, security review (~30 days) |
| **Proof of Value (POV)** | Highest | Structured evaluation with business outcomes | Success criteria, mutual action plan, measurable results |

### Step 3: Design the narrative arc

Structure the demo in three acts, using Tell-Show-Tell rhythm within each scene:

```
[Pain Resonance 20%] → [Solution Walkthrough 60%] → [Wow Moment 20%]
```

Each scene follows **Tell → Show → Tell**:
1. **Tell** — Set context: remind them of the problem they shared. *"You mentioned your team spends 3 hours a day on manual reconciliation..."*
2. **Show** — Demonstrate the solution linked to *their* story. Click through, enter realistic data, show the result.
3. **Tell** — Recap the impact. *"With this, your team goes from 3 hours to 15 minutes — reclaiming 13 hours per person per week."*

- **Pain Resonance**: Restate known pain points in the customer's industry language. Reference similar pain descriptions from same-industry cases.
- **Solution Walkthrough**: Demonstrate each feature, always connecting back to a previously stated pain point. The frame is "here is how we solve the problem you just described."
- **Wow Moment**: One unexpected reveal — a quantified result from a past case, a capability competitors lack, or an operation that makes the audience lean in.

**Alternative pattern: "Do the Last Thing First" (Great Demo! method)**:
Instead of building up to the outcome, start with it. Open the demo on the finished dashboard, report, or solved state that executives care about most. Then peel back layers only in response to prospect questions — "How did we get here? Let me show you."
- Best for: executive audiences, competitive situations where differentiation matters
- Contrast with the standard Pain → Solution → Wow flow: this is Outcome → Drill-down → Validation

**Persona-driven vignettes** (for Standard and Custom demos): Build the narrative around a single persona (e.g., "Sofia, the operations manager"). Walk through her daily struggle, the moment she uses your solution, and the victory. The customer is the hero; the product is the helper.

**Audience-adaptive design** (the "3 brains" model):

| Brain | Role | Demo Application |
|-------|------|-----------------|
| Reptilian | Filters for threat | Avoid creating urgency via fear; stay composed |
| Limbic | Drives emotion | Stories and vignettes hit here — make them *feel* the transformation |
| Neocortex | Handles logic | Keep it simple — this is where they *justify* the emotional decision |

- **Executive audience**: Lead with outcome comparisons, minimize clicks. Show before/after, not how.
- **Technical audience**: Add architecture walkthroughs, data flow diagrams, API call sequences.
- **Business audience**: Balance story and proof. Show the workflow, show the metric.

### Step 4: Generate the demo script

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

**Persona**: <Who is the protagonist of this scene>

**Tell** (Set context):
<Remind them of the problem — use their own language, reference discovery insights>

**Show** (Demonstrate):
1. Open <page/terminal> → enter <command/parameters>
2. Click <button/menu> → display <content>
3. ...

**Tell** (Recap impact):
<Connect what they just saw to a measurable outcome. "This means your team can..." >

**Expected Result**:
<What the screen should show>

**Fallback**:
If <step X> fails:
- Plan A: Switch to pre-recorded GIF (path: ...)
- Plan B: Verbal explanation + screenshot (path: ...)
- Plan C: Skip, transition phrase: "..."

**Composure note**: If something breaks, acknowledge it casually — "This is my demo instance acting up" — and pivot to conversation about their problem. Never panic. Prospects mirror your emotional state.

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

### Step 5: Post-generation guidance

- SE should check off the Data Preparation and Environment checklists the day before the demo
- Fallback screenshots/GIFs should be recorded before the live demo
- The demo should be tested 10 minutes before start: refresh tabs, click through, verify updates
- If the demo reveals new customer requirements, suggest running `/case-import` or updating the discovery call plan
- After the demo, the SE should send 2-3 crisp follow-up bullets the buyer can forward internally

## Design Rules

- For a 30-minute slot, live operations should not exceed 20 minutes — reserve 10 minutes for interaction and contingencies
- Fallback plans must be executable (concrete file paths, reproducible steps), not "improvise"
- Data preparation items must specify concrete content, not "prepare some test data"
- Narrative language must be conversational with pause markers (`<pause>`), not read-from-script stiff
- For executive audiences: reduce action steps, emphasize outcome comparisons. For technical audiences: add architecture and data flow explanations
- Use industry-realistic data models (even with only 20 records). Never use placeholder values like `test_user_1`
- **Composure principle**: prospects mirror your emotional state. If things break, acknowledge casually and pivot to conversation. Close the laptop and whiteboard if needed.
- **Post-demo follow-up**: generate 2-3 crisp bullets the buyer can use to pitch internally (assume they have 60 seconds). Tie every bullet to their stated business outcomes.

## Quality Checklist

- [ ] Demo level classified (Standard / Custom / POC / POV)
- [ ] Narrative arc follows the three-act structure, each scene has a clear purpose
- [ ] Each scene uses Tell-Show-Tell rhythm
- [ ] Persona-driven vignette present (for Standard/Custom levels)
- [ ] Each scene includes actions, expected results, and fallback plan
- [ ] Composure note present for critical failure points
- [ ] Data preparation checklist items are specific and executable
- [ ] Environment checklist covers critical dependencies
- [ ] Transition lines read naturally, not forced
- [ ] Q&A Ammo anticipates at least 3 common questions
- [ ] Post-demo follow-up bullets generated
- [ ] Total duration is reasonable (live operations at 60-70% of total)
- [ ] Output frontmatter complete
