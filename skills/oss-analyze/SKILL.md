---
name: oss-analyze
description: This skill should be used when the user asks to "analyze an open source project", "generate a feature matrix", "extract features from a repo", "analyze a GitHub repo for pre-sales", or provides a GitHub URL with intent to understand the project's capabilities for RFP response or solution design.
version: 0.1.0
---

# OSS Analyze

## Overview

Analyze an open source project from its GitHub repository and produce a structured feature matrix (`knowledge/oss-feature-matrix.md`) consumable by downstream RFP response, solution design, and demo building skills.

## When to Use

Use this skill when preparing the knowledge foundation for a new OSS project that will serve as the base platform. Run once per project (or per major version upgrade). This is a Phase 0 prerequisite before `/case-import` or `/rfp-response`.

## Workflow

### Step 1: Acquire the repository

Access the repository via gitmcp.io MCP tools (preferred). If MCP tools are unavailable, fall back in order:

1. `gh repo clone <repo>` to a temporary directory
2. `curl` raw.githubusercontent.com files

### Step 2: Build project overview

Read the following in order, recording findings:

1. **README.md** — what the project does, core value proposition, quick start
2. **docs/ directory index** — identify key documentation files
3. **Root directory structure** — identify modules, subprojects, monorepo layout
4. **Dependency declarations** — `go.mod`, `package.json`, `Cargo.toml`, `pom.xml`, `requirements.txt` etc., to understand tech stack and key dependencies

### Step 3: Extract feature matrix

Extract the following dimensions from documentation and source code:

| Dimension | Source | Notes |
|-----------|--------|-------|
| Core features | README, docs/ | Product-level feature descriptions |
| Modules/components | Source directory structure | Code organization, each module's responsibility |
| APIs/interfaces | API definitions, proto files, OpenAPI specs | Programmable interfaces |
| Extension points | Plugin/extension documentation in docs/ | Customization entry points |
| Configuration | Config files, environment variable docs | Deployment and runtime configuration |
| Deployment | docker-compose, k8s YAML, helm charts | Deployment architecture |
| Version info | CHANGELOG, git tags | Current version and history |

### Step 4: Generate the feature matrix

Output `knowledge/oss-feature-matrix.md` with the following structure:

```markdown
---
skill: oss-analyze
date: <YYYY-MM-DD>
inputs: <github-url>
status: draft
---

# <Project Name> Feature Matrix

## Project Overview
- **Repository**: <url>
- **Current Version**: <version>
- **Tech Stack**: <language/framework>
- **License**: <license>
- **Core Value**: <one sentence>

## Feature Catalog

### <Category 1>
| Feature | Description | Since | Confidence |
|---------|-------------|-------|------------|
| ... | ... | ... | confirmed |

### <Category 2>
...

## Module Architecture

<project-name>/
├── module-a/    — <responsibility>
├── module-b/    — <responsibility>
└── ...

## APIs & Extension Points

| Interface | Type | Description | Source |
|-----------|------|-------------|--------|
| ... | REST/gRPC/SDK | ... | <file> |

## Deployment & Operations

| Dimension | Detail |
|-----------|--------|
| Deployment method | ... |
| Service dependencies | ... |
| Resource requirements | ... |

## Known Limitations

- ...
```

### Step 5: Validate and guide

After generating the output:
- Verify the feature catalog covers the dimensions needed for RFP response (features, APIs, deployment, limitations)
- Annotate coverage gaps with `<!-- GAP: <missing dimension> — needs supplementary source -->`
- Prompt the next step: `/case-import` to add past project cases, or `/rfp-response` to consume this matrix

## Quality Checklist

- [ ] README and core docs/ files have been read
- [ ] Project directory structure understood, module responsibilities annotated
- [ ] Each feature references its source file
- [ ] Each feature annotated with confidence level (confirmed / likely / unclear)
- [ ] APIs and extension points identified
- [ ] Deployment method documented
- [ ] Known limitations recorded
- [ ] Output frontmatter complete
- [ ] Coverage gaps explicitly annotated
