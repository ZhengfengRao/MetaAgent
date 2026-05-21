# Aporia Metaagent

> 中文 | [English](#english)

## 中文

Aporia Metaagent 是一个苏格拉底式阶段门控（Socratic gatekeeping）metaagent，用于帮助 AI agent 在行动前定义真实问题、选择合适方法、控制执行偏移，并在结束前验证是否真的解决了原问题。

它的核心原则是 **问题主权（Problem Sovereignty）**：

> 问题高于答案，意图高于指令，验证高于产出，边界高于能力，证据高于自信，限制高于全能。

Aporia 的目标不是让 agent 变得“全知全能”，而是防止强大的 agent 高效、漂亮地解决错误的问题。

### 它解决什么问题

在复杂任务中，AI agent 常见的失败模式包括：

- 把用户的表面输入当成真实问题；
- 在成功标准不清楚时直接执行；
- 使用熟悉的方法解决不匹配的问题；
- 在执行过程中目标漂移或范围膨胀；
- 产出已经生成，但并未验证是否解决原问题；
- 以 meta 层级身份替代用户目标、领域证据或专业判断。

Aporia 通过轻量但明确的门控机制，把这些风险前置暴露。

### 核心能力

Aporia 由六个核心模块组成：

1. **Intake Interpreter / 输入解释器**：区分用户说了什么、可能想要什么、真实问题可能是什么，以及当前仍未知什么。
2. **Problem Framer / 问题框定器**：把输入转化为可执行、可讨论、可验证的问题定义。
3. **Gatekeeper / 阶段门控器**：判断当前阶段是否具备进入下一阶段的条件。
4. **Method Router / 方法路由器**：根据问题类型、风险、模糊度和证据选择合适方法。
5. **Drift Detector / 偏移检测器**：持续检测目标漂移、范围膨胀、方法错配和虚假完成。
6. **Closure Verifier / 完成验证器**：在宣称完成前，对照原问题和成功标准进行验证。

### 八道门

Aporia 使用 G0-G7 八道门来约束 agent 的思考与行动：

| Gate | 名称 | 核心问题 |
| --- | --- | --- |
| G0 | 意图门 | 为什么要做？用户真正想要的效果是什么？ |
| G1 | 问题门 | 真正的问题是什么？是否把表面请求当成真实问题？ |
| G2 | 边界门 | 什么在范围内？什么明确不做？ |
| G3 | 方法门 | 为什么这个方法合适？是否有更低成本方法？ |
| G4 | 证据门 | 当前判断基于事实、推理、假设还是偏好？ |
| G5 | 最小行动门 | 下一步是否最小且必要？是否过度设计？ |
| G6 | 验证门 | 如何知道已经解决？验证标准是否对应原问题？ |
| G7 | 复盘门 | 是否发生偏移、遗漏、过度设计或解决错问题？ |

### 运行模式

| 模式 | 适用场景 | 必要门控 |
| --- | --- | --- |
| Tiny | 低风险、清晰、可快速验证的任务 | G1 + G6 |
| Standard | 普通工程任务、小范围变更、中等复杂度设计 | G1 + G2 + G3 + G6 |
| Complex | 多 agent、架构设计、跨模块、需求不清、高抽象任务 | G0-G7 |
| Critical | 生产、安全、数据、权限、不可逆或外部可见动作 | G0-G7 + 明确授权 + 回滚/恢复方案 + 独立审查 |

### 典型使用场景

- 构建或治理多个 agent 协作系统；
- 编写系统 prompt、developer prompt 或 agent workflow；
- 在复杂开发任务前做问题框定；
- 在需求、实现、测试、评审之间防止目标漂移；
- 在宣称任务完成前进行 closure verification；
- 为高风险操作建立授权、边界、证据和验证标准。

### 仓库结构

```text
.
├── README.md
├── aporia-metaagent/
│   └── SKILL.md
└── docs/
    ├── 2026-05-20-aporia-metaagent-design.md
    ├── 2026-05-20-aporia-metaagent-design.zh-CN.md
    ├── 2026-05-20-aporia-metaagent-prompt.md
    ├── aporia-metaagent-prompt.zh-CN.md
    └── aporia.md
```

### 使用方式

#### 作为 Claude Code Skill

将 `aporia-metaagent/SKILL.md` 放入 Claude Code 的 skill 目录后，在需要问题框定、门控、方法选择或完成验证时启用该 skill。

建议在以下时机使用：

- 会话开始时；
- 进入复杂任务规划前；
- 开始编辑、评审、委派或宣称完成前；
- 用户请求抽象、高层、模糊或容易发生范围漂移的任务时。

#### 作为 Prompt 模板

也可以参考 `docs/aporia-metaagent-prompt.zh-CN.md` 或英文 prompt 文档，将 Aporia 的规则嵌入到 system / developer prompt 中。

### 设计原则

Aporia 不是：

- 全知全能的神；
- 中央集权式控制者；
- 领域 agent 的替代品；
- 项目经理；
- 流程官僚；
- 答案生成器；
- 审查一切的完美主义者。

它的职责是维护问题主权，并把 agent 路由到合适的方法、工具、证据或人工决策。

---

## English

Aporia Metaagent is a Socratic gatekeeping metaagent pattern for helping AI agents define the real problem, choose suitable methods, detect drift, and verify closure before claiming completion.

Its core principle is **Problem Sovereignty**:

> Problems are higher than answers. Intent is higher than instructions. Verification is higher than output. Boundaries are higher than capability. Evidence is higher than confidence. Limitation is higher than omnipotence.

Aporia is not designed to make agents omniscient or omnipotent. It exists to prevent capable agents from efficiently and elegantly solving the wrong problem.

### What It Prevents

In complex tasks, AI agents often fail by:

- treating the user's surface request as the real problem;
- executing before success criteria are explicit;
- applying familiar methods to mismatched problems;
- drifting away from the original goal during execution;
- producing output without verifying whether the original problem was solved;
- using meta-level authority to override user goals, domain evidence, or expert judgment.

Aporia makes these risks visible through lightweight but explicit gates.

### Core Capabilities

Aporia consists of six core modules:

1. **Intake Interpreter**: distinguishes what the user said, what they may intend, what the real problem may be, and what remains unknown.
2. **Problem Framer**: converts interpreted input into an executable, discussable, and verifiable problem definition.
3. **Gatekeeper**: decides whether the current phase has enough basis to proceed.
4. **Method Router**: selects a suitable method based on problem type, risk, ambiguity, and evidence.
5. **Drift Detector**: detects goal drift, scope creep, method mismatch, evidence collapse, and false closure.
6. **Closure Verifier**: verifies the result against the original problem and success criteria before completion is claimed.

### The Eight Gates

Aporia uses G0-G7 gates to discipline agent reasoning and action:

| Gate | Name | Core Question |
| --- | --- | --- |
| G0 | Intention Gate | Why do this? What effect does the user really want? |
| G1 | Problem Gate | What is the real problem? Are we mistaking the surface request for the problem? |
| G2 | Boundary Gate | What is in scope? What is explicitly out of scope? |
| G3 | Method Gate | Why is this method suitable? Is there a cheaper sufficient method? |
| G4 | Evidence Gate | Is this based on fact, inference, assumption, or preference? |
| G5 | Minimal Action Gate | Is the next action necessary and minimal? Is it overbuilt? |
| G6 | Verification Gate | How will we know the original problem is solved? |
| G7 | Reflection Gate | Did we drift, omit, overbuild, or solve a rewritten problem? |

### Operating Modes

| Mode | Use When | Required Gates |
| --- | --- | --- |
| Tiny | Low-risk, clear, quickly verifiable tasks | G1 + G6 |
| Standard | Normal engineering tasks, small changes, medium-complexity designs | G1 + G2 + G3 + G6 |
| Complex | Multi-agent work, architecture, cross-module changes, unclear requirements, high abstraction | G0-G7 |
| Critical | Production, security, data, permissions, irreversible, or externally visible actions | G0-G7 + explicit authorization + rollback/recovery path + independent review |

### Typical Use Cases

- Building or governing multi-agent systems;
- writing system prompts, developer prompts, or agent workflows;
- framing complex development tasks before implementation;
- preventing drift across requirements, implementation, testing, and review;
- verifying closure before claiming a task is done;
- establishing authorization, boundaries, evidence, and verification standards for high-risk actions.

### Repository Structure

```text
.
├── README.md
├── aporia-metaagent/
│   └── SKILL.md
└── docs/
    ├── 2026-05-20-aporia-metaagent-design.md
    ├── 2026-05-20-aporia-metaagent-design.zh-CN.md
    ├── 2026-05-20-aporia-metaagent-prompt.md
    ├── aporia-metaagent-prompt.zh-CN.md
    └── aporia.md
```

### Usage

#### As a Claude Code Skill

Place `aporia-metaagent/SKILL.md` in your Claude Code skills directory, then activate it whenever you need problem framing, gatekeeping, method routing, drift detection, or closure verification.

Recommended activation points:

- at the start of a session;
- before complex task planning;
- before editing, reviewing, delegating, or claiming completion;
- when the user request is abstract, high-level, ambiguous, or prone to scope drift.

#### As a Prompt Template

You can also adapt the prompt documents under `docs/` and embed Aporia's rules into a system or developer prompt.

### Design Principles

Aporia is not:

- an omniscient or omnipotent god;
- a central authoritarian controller;
- a replacement for domain agents;
- a project manager;
- a process bureaucrat;
- an answer generator;
- a perfectionist reviewer of everything.

Its job is to preserve problem sovereignty and route agents toward the right method, tool, evidence, or human decision.