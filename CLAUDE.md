# SE Skills

Solutions Engineer 日常工作流沉淀为可复用的 Claude Code plugin。交付模式：开源项目 + 二次开发，面向内部平台。

## 架构

```
Phase 0: 知识自举
  /oss-analyze ──→ knowledge/oss-feature-matrix.md
  /case-import  ──→ knowledge/cases/<case>.md

Phase 1: 日常工作流
  /rfp-response   ──→ outputs/rfp/<customer>-rfp-draft.md
  /discovery-prep ──→ outputs/discovery/<customer>-call-plan.md
  /demo-builder   ──→ outputs/demo/<customer>-demo-script.md
```

## 从零启动

1. 运行 `/oss-analyze <github-url>` 分析开源项目，生成功能矩阵
2. 运行 `/case-import` 沉淀 3-5 个历史案例
3. 开始日常使用 Phase 1 的三个 skill

## 约定

- 产物统一放在约定路径下，skill 之间通过路径发现上下游产物
- 每个产物的 frontmatter 至少包含 `skill`, `date`, `inputs`, `status`
- skill 在信息不足时输出"已知部分 + 缺口标注"，不静默失败
- 详见 `docs/CONVENTIONS.md`

## skill 设计原则

- 一个 skill = 一个 SE 工作阶段，产出可独立审查的产物
- skill 之间不硬编码依赖，通过产物类型解耦
- 优先"骨架层"（领域通用结构），产品特定内容由知识库注入
