# DEV-SKILL：通用 AI 辅助开发工程规范

> 一套从真实项目中提炼的可复用开发原则与 Agent 技能集。
> 适用于任何需要 AI Agent 参与开发的软件项目。

---

## 快速配置指南（5 步上手）

### Step 1：复制文件到你的项目

```bash
# 在你的项目根目录下执行：

# 方式 A：整套复制（推荐首次使用）
cp /path/to/dev-skill-repo/AGENTS.md ./AGENTS.md
cp -r /path/to/dev-skill-repo/.agents ./.agents

# 方式 B：只复制你需要的 Skill
mkdir -p .agents/skills
cp /path/to/dev-skill-repo/AGENTS.md ./AGENTS.md
cp -r /path/to/dev-skill-repo/.agents/skills/git-safety ./.agents/skills/
cp -r /path/to/dev-skill-repo/.agents/skills/three-strike-fix ./.agents/skills/
# ...按需添加更多
```

复制完成后，你的项目结构应该是：

```
your-project/
├── AGENTS.md                    ← 工程宪法（Agent 会自动读取）
├── .agents/
│   └── skills/
│       ├── acceptance-matrix/SKILL.md
│       ├── change-classification/SKILL.md
│       └── ... (你选择的 Skill)
├── docs/                        ← 下一步创建
│   ├── DEVELOPMENT_ROADMAP.md
│   ├── PROJECT_STATE.md
│   └── CURRENT_TASK.md
└── (你的项目源码...)
```

### Step 2：创建三份活动文档

这三份文档是 Agent 跨会话的唯一状态来源，必须在你项目的 `docs/` 目录下创建。

**① `docs/DEVELOPMENT_ROADMAP.md`（规划入口）**

```markdown
# Development Roadmap

## 项目简介
[你的项目是做什么的，一两句话]

## 技术栈
[语言、框架、构建工具、关键依赖]

## 模块规划
| 优先级 | 模块 | 状态 | 说明 |
|--------|------|------|------|
| P0 | 核心功能 | 进行中 | ... |
| P1 | 次要功能 | 待开始 | ... |

## 关键决策记录
| 日期 | 决策 | 原因 |
|------|------|------|
| YYYY-MM-DD | 选择了 X 而非 Y | 因为 Z |
```

**② `docs/PROJECT_STATE.md`（状态入口）**

```markdown
# Project State

## 当前门禁
`IN_PROGRESS` / `ARCHITECTURE_REVIEW` / `PASS` / `BLOCKED`

## 当前已验证快照
- 构建状态：✅ 通过 / ❌ 失败
- 单元测试：X/Y 通过
- 目标设备验证：未开始 / 模拟器 / 真机

## 未解决风险
| 风险 | 影响 | 当前状态 |
|------|------|----------|
| ... | ... | ... |

## 下一步允许执行的动作
- [ ] 具体可以做的下一件事
```

**③ `docs/CURRENT_TASK.md`（任务入口）**

```markdown
# Current Task

## 任务 ID
TASK-YYYYMMDD-001

## 任务目标
[这个任务要完成什么]

## 验收矩阵

### 主链路
[用户真正需要完成的一条端到端路径]

### 必须证据
| 证据类型 | 具体内容 | 状态 |
|----------|---------|------|
| 单元测试 | ... | 待获取 |
| 截图 | ... | 待获取 |
| 设备验证 | ... | 待获取 |

### 禁止事项
- 不得修改：[列出不能动的模块]
- 不得使用：[列出不能用的 Demo/fallback]

### 退出状态
[全部满足才能标记 PASS 的具体条件]

### 未完成状态
[证据缺失时使用什么状态：PARTIAL_PASS / BLOCKED / HUMAN_DECISION]

## 进度记录
| 时间 | 动作 | 结果 |
|------|------|------|
| ... | ... | ... |
```

### Step 3：根据你的项目定制 AGENTS.md

AGENTS.md 是通用的，但有几个地方需要你按项目实际情况修改：

| 需要定制的内容 | 在 AGENTS.md 中的位置 | 改成什么 |
|---------------|---------------------|----------|
| 回归验证命令 | §9.6 验证与证据等级 | 改成你项目的测试命令（如 `npm test`、`pytest`、`go test ./...`） |
| 证据等级定义 | §9.6 | 按你的项目调整（Web 项目可能没有 DEVICE_VERIFIED，改为 PRODUCTION_VERIFIED） |
| 真机/生产操作授权 | §0 | 改成你的部署环境描述 |
| 活动文档路径 | §6 | 如果你的文档不在 `docs/` 下，改成实际路径 |

**示例：Web 项目的证据等级调整**

```markdown
# 原文（移动端）：
BUILD_VERIFIED < COMPONENT_VERIFIED < SIMULATOR_VERIFIED < DEVICE_VERIFIED

# 改为（Web 项目）：
BUILD_VERIFIED < COMPONENT_VERIFIED < STAGING_VERIFIED < PRODUCTION_VERIFIED
```

### Step 4：配置 AI Agent 读取规范

根据你使用的 AI 工具，配置方式不同：

**Cursor / Qoder（推荐）**

AGENTS.md 放在项目根目录后，Agent 会自动读取。Skill 文件在 `.agents/skills/` 下也会被自动发现。无需额外配置。

**Codex / ChatGPT Codex**

在项目根目录放置 AGENTS.md 即可，Codex 启动时会自动加载。

**其他 AI 编码工具**

如果工具不支持自动读取 AGENTS.md，将以下内容注入到系统提示（System Prompt）中：

```
你是一个严格遵循工程宪法的开发助手。
开始任何工作前，先读取并遵守 AGENTS.md 中的所有规则。
遇到具体场景时，读取对应的 .agents/skills/<skill-name>/SKILL.md 获取详细指导。
每次修改代码前，确认验收矩阵已冻结。
每次提交前，检查 Git 安全规则。
遇到不确定的决策，检查人工决策边界。
```

### Step 5：开始使用

配置完成后，给 Agent 下达任务时只需正常描述需求，Agent 会自动：

```
你：「帮我修复这个 Bug：用户点击导出后没反应」

Agent 内部自动执行的流程：
  │
  ├── 1. 读取 AGENTS.md → 知道所有规矩
  ├── 2. 判断任务类型 → Bug 修复 → 加载 three-strike-fix Skill
  ├── 3. 判断变更级别 → S1（边界清楚的 Bug）
  ├── 4. 写验收矩阵 → 记录到 CURRENT_TASK.md
  ├── 5. 建立 checkpoint commit → 按 git-safety 执行
  ├── 6. 开始修复 → 第一次尝试 → 装机验证
  ├── 7. 如果 3 次没修好 → 自动转入根因分析
  └── 8. 修好后 → 更新三份文档 → 输出最终汇报
```

你不需要手动告诉 Agent "用哪个 Skill"，它会根据场景自动匹配。

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
