# se-skills

Claude Code plugin for Solutions Engineers. Codifies daily pre-sales workflows — from open source analysis to RFP response — into reusable slash commands.

Designed for the **OSS + customization** delivery model.

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

| Phase | Skill | Trigger | Output |
|-------|-------|---------|--------|
| 0 — Foundation | `/oss-analyze` | GitHub repo URL | `knowledge/oss-feature-matrix.md` |
| 0 — Foundation | `/case-import` | Project narrative (text) | `knowledge/cases/<case>.md` |
| 1 — Daily | `/rfp-response` | RFP document (.pdf/.docx/.md) | `outputs/rfp/<customer>-rfp-draft.md` |
| 1 — Daily | `/discovery-prep` | Customer context | `outputs/discovery/<customer>-call-plan.md` |
| 1 — Daily | `/demo-builder` | Scenario + features | `outputs/demo/<customer>-demo-script.md` |

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

## Skill Details

### `/oss-analyze`

Analyze an open source project's GitHub repository (via gitmcp.io MCP or `gh` CLI fallback) and produce a structured feature matrix: capabilities, module architecture, APIs, extension points, deployment requirements, and known limitations.

Re-run when the OSS project releases a new major version.

### `/case-import`

Convert a natural-language project narrative from an SE into a standardized case file. The skill asks targeted follow-up questions to fill in required fields (customer, industry, pain points, solution, pitfalls, results) and outputs a structured markdown file.

### `/rfp-response`

Parse a customer RFP/RFI document, match each requirement against the OSS feature matrix and historical case library, and generate a categorized response draft. Each response is labeled with coverage status (COVERED_OSS / COVERED_CUSTOM / PARTIAL / GAP) and traced to its source.

### `/discovery-prep`

Generate a structured call plan for technical discovery meetings. Includes a customer profile, testable hypotheses, a three-phase question path (current state → pain points → solution probing), an "ammo belt" of relevant cases and capabilities, and specific topics to avoid.

### `/demo-builder`

Generate a structured demo script from customer scenario and feature requirements. Output includes scene-by-scene narrative (pain resonance → solution walkthrough → wow moment), step-by-step actions, expected results, fallback plans per scene, and environment/data preparation checklists.

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
