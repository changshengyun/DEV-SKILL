# DEV-SKILL：通用 AI 辅助开发工程规范

> 一套从真实项目中提炼的可复用开发原则与 Agent 技能集。
> 适用于任何需要 AI Agent 参与开发的软件项目。

---

## 这是什么

本项目包含两部分：

1. **AGENTS.md** — 工程宪法，定义了 Agent 在任何项目中必须遵守的底线规则
2. **Skills** — 15 个可独立调用的技能模块，每个解决一类具体问题

你可以把整套规范直接复制到新项目中使用，也可以按需挑选其中几个 Skill。

---

## 目录结构

```
dev-skill-repo/
├── README.md                                    ← 你正在看的文件
├── AGENTS.md                                    ← 工程宪法（通用规则）
└── .agents/
    └── skills/
        ├── acceptance-matrix/SKILL.md           ← 验收矩阵先行
        ├── change-classification/SKILL.md       ← 变更分级（S0/S1/S2）
        ├── three-strike-fix/SKILL.md            ← 三次修复熔断 + 根因分析
        ├── evidence-first-debugging/SKILL.md    ← 证据优先调试
        ├── git-safety/SKILL.md                  ← Git 安全协议
        ├── human-decision-boundary/SKILL.md     ← 人工决策边界
        ├── ai-sandbox-isolation/SKILL.md        ← AI 改动沙箱隔离
        ├── ai-llm-integration/SKILL.md          ← AI/LLM 集成原则
        ├── ai-evaluation/SKILL.md               ← AI 测评框架
        ├── error-handling/SKILL.md              ← 错误处理模式
        ├── build-deploy-verify/SKILL.md         ← 构建部署验证
        ├── documentation-standards/SKILL.md     ← 文档标准
        ├── architecture-change-gate/SKILL.md    ← 架构变更门禁
        ├── project-architecture-gate/SKILL.md   ← 项目架构评审门禁
        └── mvp-implementation-gate/SKILL.md     ← MVP 实现门禁
```

---

## 21 条核心原则速查

| 编号 | 原则 | 对应 Skill | 一句话 |
|------|------|-----------|--------|
| P1 | 验收矩阵先行 | `acceptance-matrix` | 写清楚"什么算完成"再动手 |
| P2 | 变更分级 | `change-classification` | 按风险决定验证力度 |
| P3 | 三次修复熔断 | `three-strike-fix` | 快试有上限，过了就停下来分析 |
| P4 | 编译 ≠ 验收 | `evidence-first-debugging` | 必须在真实环境走真实入口 |
| P5 | Git 精确暂存 | `git-safety` | 只提交该提交的，禁止破坏性操作 |
| P6 | 人工决策边界 | `human-decision-boundary` | 划清"自己能定"和"必须问人" |
| P7 | 本地验证说了算 | `ai-llm-integration` | AI 判断不可全信，本地规则做裁决 |
| P8 | 沙箱隔离再合入 | `ai-sandbox-isolation` | AI 改动先在副本验证 |
| P9 | 敢猜 + 下游兜底 | `ai-llm-integration` | 上游不弃权，下游严控 |
| P10 | 黄金集 + 多轮统计 | `ai-evaluation` | AI 评测靠基准集和多次运行 |
| P11 | 错误提示不被门控吞 | `error-handling` | 错误显示独立于运行状态 |
| P12 | 异常消息脱敏 | `error-handling` | 密钥/Token/路径不进日志 |
| P13 | 降级白名单 | `error-handling` | 只有列出的错误才允许降级 |
| P14 | 取消 ≠ 失败 | `error-handling` | 用户取消有独立状态 |
| P15 | 装机前检完整性 | `build-deploy-verify` | 可选组件必须验证是否打包 |
| P16 | 高吞吐弃轮询 | `build-deploy-verify` | 日志太快就换数据源 |
| P17 | 文档格式统一 | `documentation-standards` | 文本统一 .md，二进制分离 |
| P18 | 三份文档唯一状态源 | `documentation-standards` | 规划/状态/任务各一份 |
| P19 | Bug 登记标准化 | `documentation-standards` | 七字段模板跟代码一起提交 |
| P20 | 重大变更书面提案 | `architecture-change-gate` | 证据+影响+备选+回滚+建议 |
| P21 | 状态驱动优于异常传播 | `error-handling` | 错误写进状态，UI 只读 |

---

## 整体运转流程

```
┌─────────────────────────────────────────────────────────────┐
│                    项目启动                                   │
│                                                             │
│  1. 复制 AGENTS.md 到项目根目录                               │
│  2. 复制 .agents/skills/ 到项目中                             │
│  3. 创建 docs/ 目录，建立三份活动文档：                         │
│     ├── DEVELOPMENT_ROADMAP.md  (规划入口)                    │
│     ├── PROJECT_STATE.md        (状态入口)                    │
│     └── CURRENT_TASK.md         (任务入口)                    │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    收到新任务                                 │
│                                                             │
│  ┌─────────────────────────────────────────────────┐        │
│  │ Step 1: 读取 AGENTS.md + 三份活动文档              │        │
│  │ Step 2: 核对 Git 状态、分支、环境门禁               │        │
│  └─────────────────────┬───────────────────────────┘        │
│                        │                                    │
│                        ▼                                    │
│  ┌─────────────────────────────────────────────────┐        │
│  │ Step 3: 判断任务类型，选择 Skill                    │        │
│  │                                                  │        │
│  │  新功能/新子系统 ──▶ project-architecture-gate     │        │
│  │  已批准路线的 MVP ──▶ mvp-implementation-gate      │        │
│  │  遇到故障/失败 ──▶ evidence-first-debugging        │        │
│  │  AI 功能改动 ──▶ ai-sandbox-isolation              │        │
│  │  Bug 修复 ──▶ three-strike-fix                    │        │
│  └─────────────────────┬───────────────────────────┘        │
│                        │                                    │
│                        ▼                                    │
│  ┌─────────────────────────────────────────────────┐        │
│  │ Step 4: 写验收矩阵（acceptance-matrix）            │        │
│  │         冻结矩阵 → 建立 checkpoint commit         │        │
│  └─────────────────────┬───────────────────────────┘        │
│                        │                                    │
│                        ▼                                    │
│  ┌─────────────────────────────────────────────────┐        │
│  │ Step 5: 执行开发                                   │        │
│  │         按 change-classification 分级验证           │        │
│  │         按 git-safety 管理提交                      │        │
│  │         遇边界问题查 human-decision-boundary        │        │
│  └─────────────────────┬───────────────────────────┘        │
│                        │                                    │
│                        ▼                                    │
│  ┌─────────────────────────────────────────────────┐        │
│  │ Step 6: 验证 & 收尾                                │        │
│  │         按 build-deploy-verify 验证产物             │        │
│  │         逐项对照验收矩阵                            │        │
│  │         更新三份活动文档                             │        │
│  │         输出最终汇报                                │        │
│  └─────────────────────────────────────────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

---

## Skill 调用时机速查

| 你遇到了什么 | 用哪个 Skill |
|-------------|-------------|
| 开始一个新阶段，需要定义"完成"标准 | `acceptance-matrix` |
| 不确定一个改动需要多少验证 | `change-classification` |
| Bug 修了两三次还没好 | `three-strike-fix` |
| 构建失败、运行崩溃、测试不过 | `evidence-first-debugging` |
| 要提交代码了 | `git-safety` |
| 不确定这件事能不能自己决定 | `human-decision-boundary` |
| 要改 AI/LLM 相关的功能 | `ai-sandbox-isolation` |
| 在项目中集成 LLM/AI 服务 | `ai-llm-integration` |
| 要评估 AI 功能的质量 | `ai-evaluation` |
| 设计错误处理逻辑 | `error-handling` |
| 构建完要部署了 | `build-deploy-verify` |
| 要写项目文档或 Bug 报告 | `documentation-standards` |
| 有人想换框架/换技术栈 | `architecture-change-gate` |

---

## 如何使用

### 方式一：整套引入

```bash
# 复制到你的项目
cp -r dev-skill-repo/AGENTS.md your-project/
cp -r dev-skill-repo/.agents your-project/
```

然后在你的项目中创建 `docs/` 目录和三份活动文档。

### 方式二：按需挑选

只复制你需要的 Skill：

```bash
# 只需要 Bug 修复和 Git 安全
cp -r dev-skill-repo/.agents/skills/three-strike-fix your-project/.agents/skills/
cp -r dev-skill-repo/.agents/skills/git-safety your-project/.agents/skills/
```

### 方式三：作为 AI Agent 指令

将 AGENTS.md 和选定的 Skill 文件内容注入到 AI Agent 的系统提示中，Agent 就会按照这些规则执行开发任务。

---

## 来源

这套规范提炼自 **lyric-captioner-android** 项目的实际开发经验。该项目是一个 Android 视频字幕编辑器，集成了 Whisper 语音识别和 DeepSeek AI 增强功能。在开发过程中积累了大量工程实践、踩坑经验和决策记录，现将其中可通用的部分提取为这套规范。

---

## 许可

本规范自由使用，无需署名。按你的项目需要修改即可。
