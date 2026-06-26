---
name: build-loop-engineer-system
description: 一键将当前项目构建为完整的 Loop-Engineer 多 Agent 协作开发环境。自动扫描项目语言/框架/结构，生成适配的 CLAUDE.md、Agent、Skill、配置、测试和文档。
---

# Build Loop Engineer System — 一键构建框架

将当前项目构建为完整的 Loop-Engineer 多 Agent 协作开发环境。

**核心原则**：先扫描项目，再写文件。CLAUDE.md 必须填入真实的项目信息，不留占位符。

## 输入
- 项目根目录路径（默认为当前工作目录）

## 执行步骤

### Step 1: 扫描目标项目（必须先执行，不可跳过）
- [ ] 1.1 列出根目录文件和目录结构（`ls -la`，识别顶层目录）
- [ ] 1.2 检测语言：按优先级查找 `pyproject.toml` / `package.json` / `go.mod` / `Cargo.toml` / `pom.xml` / `build.gradle`
- [ ] 1.3 检测框架：从包配置的 dependencies 中识别（FastAPI/Flask/React/Vue/Gin/Echo/Actix 等）
- [ ] 1.4 检测包管理器：pip / poetry / npm / pnpm / yarn / cargo / go mod
- [ ] 1.5 检测测试框架：pytest / jest / go test / cargo test
- [ ] 1.6 检测代码质量工具：ruff / black / eslint / prettier / golangci-lint / clippy
- [ ] 1.7 检测目录约定：是否有 src/ / tests/ / docs/ / scripts/ / cmd/
- [ ] 1.8 读取已有 CLAUDE.md（如有）— 保留用户填写的内容
- [ ] 1.9 读取 git 状态：`git log --oneline -5`，`git status --short`
- [ ] 1.10 构建 `project_profile`：
  ```
  name: string (from package config or directory name)
  language: "python" | "typescript" | "go" | "rust" | "java" | "unknown"
  framework: string (detected or "none")
  package_manager: "pip" | "poetry" | "npm" | "pnpm" | "yarn" | "cargo" | "go mod" | "unknown"
  test_framework: string (detected or "unknown")
  lint_tools: string[] (detected)
  has_src_dir: boolean
  has_tests_dir: boolean
  has_docs_dir: boolean
  has_claude_md: boolean
  has_git: boolean
  existing_claude_md_content: string | null
  ```

### Step 2: 创建目录结构
- [ ] `.claude/agents/` — Agent 定义
- [ ] `.claude/skills/` — Skill 定义
- [ ] `.claude/workflows/` — 隔离工作区
- [ ] `docs/` — 文档（如不存在）
- [ ] `tests/` — 测试（如不存在）

### Step 3: 写入 Loop 引擎配置
- [ ] `.claude/settings.json` — 框架配置（agents、hooks、permissions）
- [ ] `.claude/loop.md` — 可执行 loop 指令（STEP 0-7 + 异常处理场景 A-G）

### Step 4: 写入 Agent 定义（7 + 1 README）
- [ ] `.claude/agents/README.md` — Agent 体系说明
- [ ] `.claude/agents/triage.md` — 项目分诊员（含强制清晰度门控）
- [ ] `.claude/agents/orchestrator.md` — 流水线调度者（含澄清验证）
- [ ] `.claude/agents/architect.md` — 架构设计师
- [ ] `.claude/agents/planner.md` — 任务规划器
- [ ] `.claude/agents/generator.md` — 代码生成者
- [ ] `.claude/agents/evaluator.md` — 代码评判者
- [ ] `.claude/agents/requirement-clarifier.md` — 需求澄清者（上下文推理引擎）

### Step 5: 写入 Skill 定义（6 + 1 README）
- [ ] `.claude/skills/README.md` — 技能库说明
- [ ] `.claude/skills/project-scan.md` — 项目扫描
- [ ] `.claude/skills/code-quality.md` — 代码质量审查
- [ ] `.claude/skills/project-init.md` — 项目初始化
- [ ] `.claude/skills/git-worktree.md` — Worktree 管理
- [ ] `.claude/skills/requirement-clarifier.md` — 需求澄清
- [ ] `.claude/skills/build-loop-engineer-system.md` — 框架构建（本文件，不覆盖）

### Step 6: 写入项目基础文件

**6.1 CLAUDE.md**（关键：必须填入 Step 1 扫描到的真实信息）

如果项目已有 CLAUDE.md：
- 保留用户填写的内容
- 仅补充缺失的 Loop-Engineer 相关 section（Agent 体系、目录约定、开发工作流）

如果项目没有 CLAUDE.md，按模板生成，**不留 `[请填写]` 占位符**：
```markdown
# {project_profile.name}

## 项目定位
> {从 package.json description / pyproject.toml description / 目录名 推断}

## 技术栈
- **语言**：{project_profile.language}
- **运行时/框架**：{project_profile.framework}
- **包管理器**：{project_profile.package_manager}

## 开发工作流（Loop-Engineer）
...（标准 Loop-Engineer 工作流描述）

## Agent 体系
...（标准 Agent 表格）

## 目录约定
...（基于 project_profile.has_src_dir 等动态生成）

## 规范
- {语言}: 遵循 {检测到的 lint 工具配置}
- 命名：{从已有代码中推断命名风格}
- 提交：遵循 conventional commits（feat:/fix:/docs:/refactor:）
```

**6.2 requirements.md** — 需求入口（标准模板）

**6.3 loop-state.md** — 持久化记忆（标准模板，含操作规范）

**6.4 CHANGELOG.md** — 框架演进记录

**6.5 .gitignore** — 合并已有 .gitignore（如有），追加 Loop-Engineer 相关条目

**6.6 docs/README.md** — 文档索引

**6.7 docs/architecture.md** — 架构决策记录（含 ADR 模板）

### Step 7: 写入测试套件
- [ ] `tests/test_framework.py` — 框架自测（覆盖：Agent 定义、I/O 契约、状态文件、docs、loop 特性、Git 健康、职责分离、澄清门控）

### Step 8: 初始化 Git（如尚未初始化）
- [ ] 检查 `.git` 是否存在
- [ ] 不存在 → `git init && git add -A && git commit -m "feat: Loop-Engineer framework initialization"`
- [ ] 已存在 → `git add -A && git commit -m "feat: Loop-Engineer framework initialization"`

### Step 9: 验证
- [ ] `python -m pytest tests/test_framework.py -q` — 运行框架自测，确保全部通过
- [ ] 如果测试失败 → 修复问题 → 重新运行直到全部通过

### Step 10: 产出构建报告
按下方输出格式写入构建报告，展示给用户。

---

## 内嵌框架文件

以下所有 `@@FILE:<路径>@@` 标记表示文件边界。执行时逐文件写入对应路径。

> 注意：Agent、Skill、loop.md、settings.json 的完整内容已在本项目仓库中。
> 执行本 skill 时，从当前项目的 `.claude/` 目录读取这些文件，写入目标项目。
> 如果当前项目就是目标项目（自举），则跳过已存在的文件。

---

## 输出格式
```markdown
## Loop-Engineer 构建报告
- **目标项目**：[项目根目录]
- **项目扫描**：
  - 语言：{language}
  - 框架：{framework}
  - 包管理器：{package_manager}
  - 测试框架：{test_framework}
  - 代码质量：{lint_tools}
- **目录状态**：已创建 .claude/agents/ .claude/skills/ .claude/workflows/ docs/ tests/
- **Agent (7)**: triage / orchestrator / architect / planner / generator / evaluator / requirement-clarifier
- **Skill (6)**: project-scan / requirement-clarifier / code-quality / project-init / git-worktree / build-loop-engineer-system
- **Loop 引擎**：loop.md + settings.json
- **测试套件**：N 项框架自测
- **文档体系**：CLAUDE.md / requirements.md / loop-state.md / docs/ / CHANGELOG.md
- **自测结果**：[pytest 输出]
```
