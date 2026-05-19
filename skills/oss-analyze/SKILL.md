---
name: oss-analyze
description: This skill should be used when the user asks to "analyze an open source project", "generate a feature matrix", "extract features from a repo", "analyze a GitHub repo for pre-sales", "update the feature matrix", or provides a GitHub URL with intent to understand the project's capabilities for RFP response or solution design. Supports incremental updates on new major versions.
version: 0.2.0
---

# OSS Analyze

## Overview

Analyze an open source project from its GitHub repository and produce a structured feature matrix (`knowledge/oss-feature-matrix.md`) consumable by downstream RFP response, solution design, and demo building skills. Treat the feature matrix as version-controlled documentation (Docs-as-Code pattern).

## When to Use

- **First run**: when onboarding a new OSS project as the base platform for customer engagements
- **Incremental update**: when the OSS project releases a new major version — re-run to diff and update
- This is a Phase 0 prerequisite before `/case-import` or `/rfp-response`

## Workflow

### Step 0: Check for existing matrix

If `knowledge/oss-feature-matrix.md` already exists:
- Read it to understand the previous analysis baseline
- Note the previous version and date
- After generating the new analysis, highlight changes from the previous version in a `## Changes from v<prev>` section

### Step 1: Acquire the repository

**Preferred: gitmcp.io MCP.** gitmcp.io provides a hosted MCP server for GitHub repos. Configure it as an MCP server in Claude Code, then use the available tools to explore the repo:

- Use the MCP server's `get_file_tree` or equivalent to list the repo structure
- Use `get_file_content` or equivalent to read specific files
- The MCP server URL follows the pattern `https://gitmcp.io/{owner}/{repo}`

**Fallback chain** (if gitmcp.io MCP is not configured):
1. `gh repo clone <repo>` to a temporary directory
2. `curl` raw.githubusercontent.com files
3. Direct `git clone` as a last resort

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

## Changes from <previous version>
> Only present when updating an existing matrix

| Dimension | Previous | Current | Impact |
|-----------|----------|---------|--------|
| New features | — | ... | ... |
| Deprecated | ... | — | ... |
| API changes | ... | ... | ... |
```

### Step 5: Validate and guide

After generating the output:
- Verify the feature catalog covers the dimensions needed for RFP response (features, APIs, deployment, limitations)
- Annotate coverage gaps with `<!-- GAP: <missing dimension> — needs supplementary source -->`
- If this is an update, call out specific changes that affect RFP responses (new capabilities to leverage, deprecated features to avoid)
- Prompt the next step: `/case-import` to add past project cases, or `/rfp-response` to consume this matrix

## Quality Checklist

- [ ] Existing matrix checked and baseline noted (if applicable)
- [ ] README and core docs/ files have been read
- [ ] Git tags checked for accurate version info
- [ ] Project directory structure understood, module responsibilities annotated
- [ ] Each feature references its source file
- [ ] Each feature annotated with confidence level (confirmed / likely / unclear)
- [ ] APIs and extension points identified
- [ ] Deployment method documented
- [ ] Known limitations recorded
- [ ] Changes from previous version documented (if updating)
- [ ] Output frontmatter complete
- [ ] Coverage gaps explicitly annotated
