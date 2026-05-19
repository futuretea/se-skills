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
status: 已上线 | POC | 已交付
---
```

## skill 文件规范

- 路径：`skills/<skill-name>/SKILL.md`
- 目录名用 kebab-case，与 `plugin.json` 中声明的路径一致
- 每个 skill 只产出其声明的产物类型
- 信息不足时输出缺口标注 `<!-- GAP: 需要补充... -->`，不静默失败
