<p align="center">
  <a href="README.md">🏠 首页</a> | <a href="README_EN.md">🇺🇸 English</a>
</p>

---

# 🔁 Build-Loop-Engineer-System — 多 Agent 自主开发框架

<p align="center">
  <b>一键将任何项目构建为完整的多 Agent 协作开发环境</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-Claude%20Code-blue" alt="Platform: Claude Code">
  <img src="https://img.shields.io/badge/agents-7-orange" alt="7 Agents">
  <img src="https://img.shields.io/badge/skills-6-green" alt="6 Skills">
  <img src="https://img.shields.io/badge/tests-80%2B-brightgreen" alt="80+ Tests">
  <img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="License: MIT">
</p>

---

## 🧠 这是什么？

Build-Loop-Engineer-System 是一个基于 Claude Code 构建的**自主开发框架**。它将你的项目转化为一条自动化开发流水线：**7 个专业 AI Agent** 协作完成「需求 → 设计 → 实现 → 验证」的全流程。

你只需要写需求，Agent 团队会自己想办法把代码写出来。

```
你 (人类)                       Agent 团队 (AI)
   │                                │
   ├─ 写需求 ────────────────────→ triage 发现 + 调度
   │                                │
   │                        ┌─ 详细需求 → architect 设计
   │                        │                 │
   │                        │              planner 拆解
   │                        │                 │
   │                        └─ 模糊需求 → clarifier 澄清 ──→ architect 设计
   │                                                          │
   │                                                       generator 实现 ←──┐
   │                                                          │               │
   │                                                       evaluator 审查 ──┘ FAIL (≤3次)
   │                                                          │
   │                                                       PASS → 合并 ✓
   │                                                          │
   └─ 查看结果 ←─────────────────────────────────────── 更新 loop-state.md
```

## 🎯 核心原则

| 原则 | 说明 |
|------|------|
| 🔴 **写码者 ≠ 审码者** | 写代码的 Agent 绝不允许审查自己的代码 |
| 🔴 **持久化先于响应** | 每一步操作先写入 `loop-state.md`，再做其他事 |
| 🔴 **动手 &gt; 读代码** | Evaluator 必须实际运行测试，不能只读代码 |
| 🟡 **三次驳回 = 人工介入** | 同一任务被 evaluator 驳回 3 次后停止自动重试 |
| 🟡 **隔离工作区** | Generator 在独立的 git worktree 中工作，绝不碰主分支 |
| 🟡 **追加不覆盖** | 状态文件只追加，历史记录永不删除 |

## 🤖 Agent 团队

| Agent | 角色 | 做什么 |
|-------|------|--------|
| **triage** | 🕵️ 项目经理 | 扫描项目，发现该做的事，分派任务。检测模糊需求并路由给 clarifier。 |
| **requirement-clarifier** | 🔍 需求分析师 | 上下文推理引擎。从 CLAUDE.md、代码库、git 历史中挖掘信息，问用户之前先尽力自己推断。产出带置信度标注的下游就绪规格。 |
| **architect** | 🏗️ 架构师 | 设计方案（做什么 & 为什么这样做）。定义接口。记录架构决策。绝不写代码。 |
| **planner** | 📋 技术主管 | 把设计方案拆成可独立执行的任务。定义依赖和验收标准。 |
| **generator** | 💻 开发者 | 在隔离 worktree 中写代码。实现前执行预检清单。写完不自我评价。 |
| **evaluator** | 🔬 QA / 审查者 | 独立审查所有代码。执行 10 项评判清单。默认假设：代码是坏的，必须自证清白。 |
| **orchestrator** | 🎭 流水线调度 | 串联全流程。管理 worktree、合并代码、持久化状态、追踪 Token 用量。 |

## 🔄 自主循环 — STEP 0 → 6

每轮 loop 执行 8 个标准化步骤（STEP 0-7）：

```
STEP 0: 环境自检              (~10秒)   运行框架自测、检查 git 状态、恢复上下文
STEP 1: 项目扫描 (triage)      (~30秒)   发现新需求、扫描项目健康度、分派任务
STEP 1.5: 需求澄清 (clarifier) (~3分钟/轮) 上下文推理引擎，最多 2 轮
STEP 2: 需求设计 (architect+planner) (~2分钟) 设计方案、拆解任务
STEP 3: 代码实现 (generator)   (~5分钟/任务) 在隔离 worktree 中实现
STEP 4: 独立审查 (evaluator)   (~3分钟/任务) 10 项评判清单，对抗性审查
STEP 5: 持久化                 (~15秒)   提交代码、更新状态文件
STEP 6: 元认知日志              (~30秒)   反思：哪里做得好？下次如何改进？
STEP 7: 构建报告               (~15秒)   产出构建报告，展示给用户
```

## 📦 安装的文件

```
你的项目/
├── .claude/
│   ├── agents/                    ← 7 个 Agent 定义（含 I/O 契约）
│   │   └── README.md              ← Agent 体系说明文档
│   ├── skills/                    ← 6 个 Skill 定义（含可执行步骤 + README）
│   │   └── README.md              ← 技能库说明文档
│   ├── workflows/                 ← 隔离 git worktree 工作区
│   │   └── README.md
│   ├── loop.md                    ← 可执行 loop 引擎指令
│   └── settings.json              ← 框架配置 + hooks + agent 注册表
├── docs/
│   ├── README.md                  ← 文档索引
│   └── architecture.md            ← ADR 模板 + 架构决策记录
├── tests/
│   └── test_framework.py          ← 80+ 项框架自测
├── requirements.md                ← 需求入口（你在这里写需求）
├── loop-state.md                  ← 持久化记忆（Agent 跨轮记忆）
├── CLAUDE.md                      ← 项目知识库
├── CHANGELOG.md                   ← 框架演进日志
└── .gitignore                     ← 预配置的忽略规则
```

## 🚀 快速开始

### 1. 安装 Skill

将 `build-loop-engineer-system` skill 添加到你的 Claude Code skills 目录：

```bash
# 克隆或将 skill 复制到你的 skills 文件夹
cp -r loop-engineer ~/.claude/skills/build-loop-engineer-system
```

### 2. 初始化项目

进入你的项目根目录，调用 skill：

```
/build-loop-engineer-system
```

几秒钟内完成整个框架的搭建。

### 3. 写需求

编辑 `requirements.md`，在 `## 🆕 新需求` 下添加任务：

```markdown
## 🆕 新需求
- [ ] 添加用户登录功能，支持邮箱+密码登录
- [ ] 实现 REST API 的分页查询
```

需求写得模糊也没关系 — **requirement-clarifier** Agent 会自动从上下文推断，只问真正需要问的问题。

### 4. 启动 Loop

| 指令 | 行为 |
|------|------|
| `运行 orchestrator` | 手动执行一轮 |
| `/loop 10m` | 每 10 分钟自动执行一轮 |

### 5. 查看结果

- `loop-state.md` — 查看每轮执行状态
- `git log` — 查看代码变更历史
- `docs/loop-round-N-changelog.md` — 每轮改动报告

## 🔧 Skill 技能库

| Skill | 调用者 | 步骤数 | 用途 |
|-------|--------|--------|------|
| `project-scan` | triage | 5 | 扫描项目结构、技术栈、健康度 |
| `code-quality` | evaluator | 7 | 多维度代码质量审查，分级输出 |
| `project-init` | architect | 6 | 为新项目搭建标准目录和配置 |
| `git-worktree` | orchestrator, generator | 5 | git worktree 全生命周期管理 |
| `requirement-clarifier` | triage | 10 | 上下文推理引擎 —— 先推断，再提问 |
| `build-loop-engineer-system` | 用户手动调用 | 10 | 一键构建完整框架 |

## 🧪 框架自测

框架包含 **80+ 项自动化测试**，覆盖：

- ✅ Agent 存在性和元数据验证
- ✅ Agent I/O 契约验证（输入 + 输出）
- ✅ 状态文件结构完整性
- ✅ Loop 引擎完整性（7 种异常场景全覆盖）
- ✅ Skill 可执行性（所有 skill 都有具体步骤）
- ✅ 配置文件有效性（JSON 可解析、所有 agent 已注册）
- ✅ Git 健康检查
- ✅ 职责分离验证（generator ≠ evaluator、architect ≠ planner）
- ✅ Requirement Clarifier 专项：评分逻辑、停止条件、自我回答协议、智能默认值引擎、问题价值计算、下游就绪验证、置信度标注、Agent-Skill 一致性

随时运行：

```bash
python -m pytest tests/test_framework.py -v
```

每轮 loop 的 STEP 0 也会自动运行测试套件。

## 🔒 职责分离

框架在 Agent 之间强制执行严格边界：

```
architect  →  设计 WHAT & WHY        (绝不写代码、绝不拆任务)
planner    →  拆解 HOW & ORDER       (绝不重新设计、绝不写代码)
generator  →  写 CODE                (绝不自我评价、隔离工作)
evaluator  →  审 CODE                (绝不写代码、默认假设代码是坏的)
```

## 🛡️ 异常处理

Loop 引擎优雅处理 7 种失败场景（A-G）：

| 场景 | 处理方式 |
|------|----------|
| **A: 本轮无事可做** | 正常结束，记录空轮 |
| **B: 设计方案无法收敛** | 驳回需求，等待人工澄清 |
| **C: Generator 出错** | 保留 worktree 供检查，最多重试 3 次 |
| **D: Evaluator 连续 3 次驳回** | 标记 `NEEDS_HUMAN`，停止自动重试 |
| **E: 用户手动中断** | 丢弃当前步骤，保留已持久化的状态 |
| **F: Clarifier 无法收敛** | 输出含缺口标注的规格，标记"待人工确认" |
| **G: 测试套件失败** | 修复后重新运行，直到全部通过 |

## 🎨 亮点：需求澄清引擎

Requirement Clarifier 是整个框架的杀手级特性。它是一个**上下文推理引擎**，能将模糊的一句话需求转化为结构化的、下游可直接使用的规格。

它不会一上来就问问题，而是：

1. **深度上下文挖掘** — 读取 CLAUDE.md、代码库结构、包配置、git 历史
2. **自我回答协议** — 对 4×4 矩阵的 16 个子维度，优先从上下文推断答案
3. **智能默认值** — 根据检测到的项目类型（web_app / cli_tool / api_service / library / data_pipeline）应用合理默认值
4. **只问高价值问题** — 计算每个问题的多维度覆盖值，每轮最多 5 个问题
5. **下游就绪检查** — 产出前验证 architect、planner、generator、evaluator 各自是否拿到了充分信息
6. **置信度全标注** — 规格中每一项都标注 `confidence: high|medium|low` 和 `source: user_stated|inferred_from_context|default_convention`

### 4×4 需求覆盖矩阵

| 维度 | 面向 Agent | 子维度 |
|------|-----------|--------|
| **D1: 架构层面** | architect | 功能范围 · 明确排除 · 用户画像 · 问题与动机 |
| **D2: 规划层面** | planner | 独立子特性 · 硬依赖 · 优先级 · MVP 定义 |
| **D3: 实现层面** | generator | 技术约束 · 现有约定 · 性能需求 · 数据契约 · 文件位置建议 |
| **D4: 验证层面** | evaluator | 正确性证明 · 测试场景 · 错误处理 · 完成定义 · 可执行验证命令 |

## 📊 Hook 配置

```json
{
  "hooks": {
    "PreLoop":      { "description": "loop 开始前：拉取最新代码、检查依赖" },
    "PostLoop":     { "description": "loop 结束后：推送变更、清理 worktree" },
    "OnPass":       { "description": "evaluator 判定 PASS 时触发" },
    "OnFail":       { "description": "evaluator 判定 FAIL 时触发" },
    "OnNeedsHuman": { "description": "连续 3 次 FAIL 后触发：人工介入提醒" }
  }
}
```

自定义这些 Hook 来接入你的 CI/CD 流水线。

## 📝 环境要求

- **Claude Code**（支持 skill 的版本）
- **Git**（用于 worktree 隔离和版本控制）
- **Python 3.9+**（仅用于框架自测；被构建的项目可以是任何语言）

## 🤝 贡献与定制

这是一个开放框架 — 按你的工作流来定制：

1. **添加新 Agent** — 在 `.claude/agents/<name>.md` 中定义，含 I/O 契约
2. **添加新 Skill** — 在 `.claude/skills/<name>.md` 中定义，含可执行步骤
3. **添加测试** — 更新 `tests/test_framework.py` 覆盖新增内容
4. **自定义 Hook** — 编辑 `.claude/settings.json` 中的 hooks

## 📄 许可证

MIT — 随意使用、魔改、发布。

---

<p align="center">
  <b>你写需求，Loop 负责剩下的。</b><br>
  <sub>为想要 AI 真正交付代码的开发者而构建 ❤️</sub>
</p>
