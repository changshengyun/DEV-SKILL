# lyric-captioner-android 项目专属开发资产

来源：`changshengyun/Generate-Videos-with-Bilingual-Subtitles` 仓库（V4.4 收尾时迁出，2026-08-29）。

## 内容

- `.agents/`：该项目 Multi-Agent 开发体制的角色提示词（orchestrator / implementation / review / state-manager / code-review-worker）与 3 个项目特化门禁 Skill（evidence-first-debugging、mvp-implementation-gate、project-architecture-gate）。
- 仓库根 `../.codex/skills/`：Codex 用 UI/设计类 Skill（edge-to-edge、mobile-android-design、requesting-code-review、ui-ux-pro-max），与本项目通用规范无冲突，故直接放在仓库根级。

## 与通用资产的关系

本目录下的 3 个 Skill 与本仓库 `.agents/skills/` 中的同名通用版**内容不同**（项目特化版带 agents/openai.yaml 配置），因此按项目命名空间隔离存放，不覆盖通用版。
