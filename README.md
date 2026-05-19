# se-skills

Claude Code plugin for Solutions Engineers. Codifies daily pre-sales workflows — from open source analysis to RFP response — into reusable slash commands. Designed for the **OSS + customization** delivery model.

Built on proven SE methodologies: **MEDDPICC**, **Tell-Show-Tell**, **Great Demo!**, **Gap Selling**, and **FAB mapping**.

## Installation

```bash
claude plugins install .
```

Or from a repository:

```bash
claude plugins install <repo-url>
```

**Prerequisites**: Claude Code with plugin support. For OSS analysis, optionally configure a [gitmcp.io](https://gitmcp.io) MCP server.

## Skills

### Phase 0 — Knowledge Bootstrapping

Run these first to seed the knowledge base. Re-run when the OSS project ships a new major version or after significant customer engagements.

| Skill | Usage | Output |
|-------|-------|--------|
| `/oss-analyze` | `/oss-analyze https://github.com/apache/flink` | `knowledge/oss-feature-matrix.md` |
| `/case-import` | `/case-import` then paste a project narrative | `knowledge/cases/<customer>-<project>.md` |

### Phase 1 — Daily Workflows

Run these on every customer engagement. Each consumes the knowledge base artifacts from Phase 0.

| Skill | Usage | Output |
|-------|-------|--------|
| `/rfp-response` | `/rfp-response docs/customer-rfp.pdf --customer "某银行"` | `outputs/rfp/<customer>-rfp-draft.md` |
| `/discovery-prep` | `/discovery-prep --customer "某零售企业" --industry 零售` | `outputs/discovery/<customer>-call-plan.md` |
| `/demo-builder` | `/demo-builder --customer "某零售企业" --scenario "实时看板" --duration 30min` | `outputs/demo/<customer>-demo-script.md` |

## Quick Start (from zero)

```
/oss-analyze https://github.com/your-oss-project
/case-import
  → paste or describe 3-5 past customer projects
/rfp-response docs/customer-rfp.pdf --customer "某银行" --industry 金融
```

## Architecture

```
Phase 0: Knowledge bootstrapping
  /oss-analyze ──→ knowledge/oss-feature-matrix.md
  /case-import  ──→ knowledge/cases/<case>.md

Phase 1: Daily workflows
  /rfp-response   ──→ outputs/rfp/<customer>-rfp-draft.md
  /discovery-prep ──→ outputs/discovery/<customer>-call-plan.md
  /demo-builder   ──→ outputs/demo/<customer>-demo-script.md
```

Downstream skills automatically discover upstream artifacts by convention. For example, `/demo-builder` will read the discovery call plan if it exists to personalize the script.

### Methodologies by Skill

| Methodology | oss-analyze | case-import | rfp-response | discovery-prep | demo-builder |
|-------------|:-----------:|:-----------:|:------------:|:--------------:|:------------:|
| MEDDPICC | — | — | — | ✓ | — |
| Gap Selling | — | — | — | ✓ | — |
| Tell-Show-Tell | — | — | — | — | ✓ |
| Great Demo! ("Last Thing First") | — | — | — | — | ✓ |
| FAB Mapping | — | — | ✓ | — | — |
| Disco-Stration | — | — | — | ✓ | — |
| Win Themes | — | — | ✓ | — | — |
| Go/No-Go Qualification | — | — | ✓ | — | — |
| Docs-as-Code | ✓ | — | — | — | — |

## Skill Details

### `/oss-analyze`

Analyze an open source project's GitHub repository (via gitmcp.io MCP or `gh` CLI fallback) and produce a structured feature matrix: capabilities, module architecture, APIs, extension points, deployment requirements, and known limitations.

Re-run when the OSS project releases a new major version.

### `/case-import`

Convert a natural-language project narrative from an SE into a standardized case file. Captures deal outcome (won/lost), competitive context, deal stage, pitfalls, and a Deal Analysis section for win/loss review — preventing tribal knowledge from walking out the door.

### `/rfp-response`

Go/No-Go qualification first, then win theme generation, then RFP parsing and knowledge-base matching. Each requirement gets a FAB (Feature → Advantage → Benefit) mapped response with coverage status and source tracing. Includes an Executive Summary (written last) and a 3-gate review process (compliance → accuracy → narrative).

### `/discovery-prep`

MEDDPICC qualification, Gap Selling questions (quantify cost of inaction), ambition-oriented questions, landmine questions for competitive positioning, and a Disco-Stration option for blended discovery+demo sessions. Generates a structured call plan with hypotheses, question path, and a post-call MEDDPICC scorecard.

### `/demo-builder`

Classifies the demo into one of four levels (Standard → Custom → POC → POV), then generates a structured script. Each scene follows Tell-Show-Tell rhythm with persona-driven vignettes. Includes the "Do the Last Thing First" (Great Demo!) alternative pattern, the composure principle for live failures, and post-demo follow-up bullets.

## Project Structure

```
se-skills/
├── .claude-plugin/plugin.json     # Plugin manifest
├── commands/                      # Slash command definitions
├── skills/                        # Skill definitions (SKILL.md each)
├── knowledge/                     # Shared knowledge base
│   └── cases/                     # Case library
├── outputs/                       # Generated artifacts
│   ├── rfp/
│   ├── discovery/
│   └── demo/
└── docs/CONVENTIONS.md            # Artifact conventions
```

## Conventions

- Artifacts are placed at fixed paths so skills can discover upstream outputs
- Every artifact includes frontmatter: `skill`, `date`, `inputs`, `status`
- Skills output "known parts + gap annotations" (`<!-- GAP: ... -->`) when information is incomplete — never silently fail
- Skills are decoupled: no hardcoded skill-to-skill dependencies, connected only through shared artifact types
- See `docs/CONVENTIONS.md` for the full specification

## License

MIT
