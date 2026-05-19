# Artifact Conventions

## 产物目录

```
knowledge/
  oss-feature-matrix.md       # /oss-analyze 产出
  cases/
    <customer>-<project>.md   # /case-import 产出

outputs/
  rfp/<customer>-rfp-draft.md
  discovery/<customer>-call-plan.md
  demo/<customer>-demo-script.md
```

## Frontmatter（必填）

每个产物文件必须包含：

```yaml
---
skill: <生成此产物的 skill 名>
date: <YYYY-MM-DD>
inputs: <逗号分隔的输入来源>
status: draft | reviewed | final
---
```

## 案例模板

```yaml
---
customer: <客户名>
industry: <行业>
date: <YYYY-QN>
oss_version: <开源版本>
modules: <涉及的功能模块>
effort: <人周>
deal_outcome: won | lost | ongoing
deal_stage: discovery | evaluation | poc | negotiation | closed
competitor: <被替代的竞品, 或胜出的竞品>
status: 已上线 | POC | 已交付
---

## 客户背景
## 痛点
## 方案
## 踩坑记录
## 结果
## 交易分析 (won/lost 时填写)
- 为什么赢/输
- 竞争态势
```

## RFP 应答模板

每个应答项遵循 FAB 结构：

```yaml
Feature: <什么能力>
Advantage: <为什么比别的方案好>
Benefit: <给客户带来的可量化结果>
```

应答草稿包含 Go/No-Go 评分卡、Win Themes（3-5 条）、覆盖概览、逐条应答、GAP 清单、Executive Summary（最后写）。

## skill 文件规范

- 路径：`skills/<skill-name>/SKILL.md`
- 目录名用 kebab-case，与 `plugin.json` 中声明的路径一致
- 每个 skill 只产出其声明的产物类型
- 信息不足时输出缺口标注 `<!-- GAP: 需要补充... -->`，不静默失败
