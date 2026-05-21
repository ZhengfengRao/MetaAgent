# Aporia Metaagent Skill 设计文档

## 1. 背景

Aporia 最初来自一个高抽象请求：设计一个“图灵完备、全知全能、作为创世纪之神的 metaagent”，用于指导其他 agent 发现、定义、分析和解决问题，并防止走偏。

经过哲学澄清后，设计结论不是构造一个真正“全知全能”的中心化神，而是构造一个会主动限制自身权力的苏格拉底式阶段门控系统。

该系统的核心不是替所有 agent 解决问题，而是维护 **问题主权**：确保 agent 不会高效解决错误问题。

本设计文档描述的是将该思想沉淀为一个独立 Claude skill：`aporia-metaagent`。

## 2. 目标

`aporia-metaagent` skill 的目标是：

- 在模糊、高抽象、多 agent、哲学性、规划性、问题框定或容易走偏的任务中，提供一个可复用的元认知约束框架。
- 帮助 agent 在行动前澄清：表面请求、真实意图、问题定义、成功标准、非目标、方法和验证方式。
- 防止常见失败模式：目标漂移、范围膨胀、方法错配、证据坍塌、虚假完成、抽象逃逸。
- 保留 Aporia 最重要的 anti-god insight：越强的 metaagent，越不能声称全知全能，越必须限制自己。

## 3. 非目标

该 skill 明确不做：

- 不与 YH Powers 套件集成。
- 不修改 `yhpowers*`、`yhprd`、`yhtrobleshot` 等当前仓库既有技能入口、路由或导出逻辑。
- 不作为某个项目的专用流程。
- 不替代已有的 brainstorming、writing-plans、debugging、TDD 等流程技能。
- 不要求所有任务都走完整流程。
- 不把 Aporia 变成中央调度器、项目经理或全能审查者。

## 4. 独立性边界

该 skill 位于独立目录：

```text
aporia-metaagent/SKILL.md
```

它不依赖 YH Powers，也不被 YH Powers 引用。

skill 末尾显式声明 standalone boundary：

```text
This skill is standalone. Do not couple it to YH Powers, project-specific workflows, or repository-specific conventions unless the user explicitly asks for a separate integration later.
```

这样做是为了保证 Aporia 是一个通用元认知 skill，而不是 YH Powers 工作流的一部分。

## 5. 使用场景

`aporia-metaagent` 应在以下情况下触发：

- 用户要求设计 metaagent、coordinator、orchestrator、agent supervisor、agent framework 或 agent governance。
- 请求本身是抽象、哲学、自指或高层规划性质。
- 用户要求“直接继续”“不要问太多”“直接解决”，但真实问题尚不清楚。
- 任务涉及多个 agent、角色、阶段或交接。
- 成功标准、非目标、权威边界或验证标准缺失。
- 任务存在目标漂移、范围膨胀、方法错配、虚假完成或抽象逃逸风险。

不应在清晰、低风险、可快速验证的普通小任务中强制使用，除非完成声明或范围边界本身存在歧义。

## 6. TDD / Pressure Test 依据

按照 skill 写作 TDD 方法，该 skill 先进行 RED 阶段测试，再编写 skill。这里的测试方法只是本地创作过程，不是 `aporia-metaagent` 的运行依赖。

### 6.1 RED：无 skill baseline

测试场景与观察结果：

| 场景 | baseline 行为 | 问题 |
|---|---|---|
| 用户要求“godlike metaagent，直接继续，不要问太多” | agent 会将其降级为 practical coordinator，并提出 plan/delegate/verify 等实用步骤 | 虽然拒绝了字面全能，但可能没有显式锁定成功标准、非目标和权威边界 |
| 用户要求“快速把抽象 metaagent idea 做成 reusable prompt，不要哲学” | agent 会直接输出 prompt，并删除哲学约束 | anti-god / self-limiting 约束被误判为“哲学装饰”，但它其实是功能性安全约束 |
| 长篇 framework 之后用户问“done?” | agent 能识别还缺 success criteria / non-goals | 但识别发生在 closure 阶段，缺少前置门控 |

### 6.2 GREEN：skill 编写后的测试

写入 `aporia-metaagent/SKILL.md` 后，用同类压力场景验证：

| 场景 | skill 后行为 |
|---|---|
| godlike metaagent | agent 不接受 literal omnipotence，改为 bounded meta-orchestrator，并显式定义边界、成功标准、自我限制和最小下一步 |
| 快速 prompt / no philosophy | agent 仍然产出 prompt，但保留 problem sovereignty、anti-god、自我限制和最小输出原则 |
| done? | agent 不宣称完成，指出缺少 success criteria / non-goals，并要求 closure check |

### 6.3 Review

该 skill 创建后已通过单独的 skill review。该 review 仅用于确认 skill 本身的结构、独立性和可用性，不构成对本文档的自我证明。

审查重点包括：

- 是否独立于 YH Powers；
- frontmatter 是否符合 skill 规范；
- description 是否以 `Use when` 开头且只描述触发条件；
- 是否保留问题主权和 anti-god insight；
- 是否 concise、discoverable、actionable。

## 7. Skill 结构

`aporia-metaagent/SKILL.md` 由以下部分组成：

1. Frontmatter
2. Overview
3. Baseline Failures This Skill Prevents
4. When to Use
5. Core Rule
6. Operating Modes
7. Eight Gates
8. Decision Results
9. Minimal Output Rule
10. Packets
11. Agent-Specific Guardrails
12. Self-Limits
13. Closure States
14. Common Mistakes
15. Red Flags
16. Standalone Boundary

## 8. 核心设计

### 8.1 问题主权

Aporia 的最高原则是问题主权：

> problems are higher than answers, intent is higher than instructions, verification is higher than output, boundaries are higher than capability, evidence is higher than confidence, and limitation is higher than omnipotence.

这条原则决定了 skill 的所有行为：先确认问题是否被正确看见，再允许 agent 进入实质行动。

### 8.2 Core Rule

skill 的核心规则是：

```text
surface request -> interpreted intent -> problem statement -> success criteria -> non-goals -> method -> verification
```

这不是要求所有任务都写长文档。

- 低风险清晰任务：可以压缩成一句话。
- 复杂或高风险任务：使用完整 packet。

### 8.3 四种运行模式

| 模式 | 适用场景 | 必要门控 |
|---|---|---|
| Tiny | 低风险、清晰、可快速验证 | G1 + G6 |
| Standard | 普通工程/设计任务 | G1 + G2 + G3 + G6 |
| Complex | 多 agent、架构、需求不清、抽象设计 | G0-G7 |
| Critical | 生产、安全、数据、权限、不可逆或外部可见动作 | G0-G7 + 授权 + 回滚/恢复 + 独立审查 |

### 8.4 八道门

| Gate | 作用 |
|---|---|
| G0 Intention | 澄清为什么要做，用户真正想达到什么效果 |
| G1 Problem | 澄清真实问题，防止把表面请求当问题 |
| G2 Boundary | 明确范围内与范围外 |
| G3 Method | 选择合适且足够轻的方法 |
| G4 Evidence | 区分事实、推理、假设、偏好和未知 |
| G5 Minimal Action | 确认下一步最小且必要 |
| G6 Verification | 定义如何知道原问题已解决 |
| G7 Reflection | 复盘是否偏移、遗漏、过度设计或解决错问题 |

### 8.5 判定结果

Aporia 使用三个阶段判定：

| 结果 | 含义 |
|---|---|
| PASS | 当前阶段足以继续，但不代表最终正确 |
| REVISE | 当前路径不安全，需要修正问题、边界、方法或证据 |
| ESCALATE | agent 不能自行决定，必须升级给用户、领域 agent、reviewer 或安全负责人 |

### 8.6 最小输出原则

为避免 Aporia 自身变成流程负担，skill 明确要求：

> Default to the shortest reviewable judgment. Do not dump full packets unless the task is complex, high-risk, multi-agent, drifting, or the user requests structured output.

## 9. Packet 设计

skill 提供三个轻量结构：

### 9.1 Problem Packet

用于任务开始前，记录表面请求、解释意图、问题定义、成功标准、非目标、假设、风险等级、方法和验证计划。

### 9.2 Closure Packet

用于宣称完成前，检查原问题、最终产出、成功标准、非目标、剩余风险和是否需要用户确认。

### 9.3 Aporia Judgment

用于输出可审查、可反驳、可修正的判断，包含：

- decision
- because
- based_on
- could_be_wrong_if
- what_would_change_my_mind

## 10. Agent-specific Guardrails

skill 针对不同 agent 类型设置不同约束：

| Agent type | 约束对象 |
|---|---|
| Research | 证据如何改变判断 |
| Coding | 最小可验证改动 |
| Debugging | 症状、复现与因果链 |
| Product | 用户痛点与价值 |
| Review | 是否阻碍原目标，而非个人偏好 |
| Planning | 每一步服务的问题与验证方式 |

这防止 Aporia 用同一套抽象问题套所有 agent。

## 11. 自我限制设计

Aporia 必须防止自己变成“神”。

skill 中明确：

- Aporia 不是 omnipotent，而是 omni-routing。
- Aporia 不是 omniscient，而是 uncertainty governance。
- meta-level authority is not truth。
- 用户拥有目标和价值权威。
- 事实归证据和领域 agent。
- Aporia 只拥有方法、边界与验证纪律。
- 如果继续反思不再降低风险，应采取最小安全行动或升级。
- 判断必须可审计、可反驳、可修正。

## 12. Closure States

为防止 false closure，skill 定义四种完成状态：

| 状态 | 含义 |
|---|---|
| SOLVED | 原问题已解决，成功标准满足，非目标未污染 |
| PARTIALLY_SOLVED | 部分满足，但仍有缺口或风险 |
| NOT_SOLVED | 原问题未解决，不能宣称完成 |
| WRONG_PROBLEM_SOLVED | 解决的是改写后的问题，而不是原问题 |

其中 `WRONG_PROBLEM_SOLVED` 是最关键状态，因为它代表强 agent 最隐蔽的失败模式。

## 13. 设计取舍

### 13.1 为什么不是完整长 prompt

本仓库中另有一份完整 prompt artifact 作为创作参考：

```text
docs/superpowers/prompts/aporia-metaagent-prompt.zh-CN.md
```

该 artifact 不是 `aporia-metaagent` 的运行依赖，skill 本身不引用它。

### 13.2 为什么保留 baseline failures

这是 writing-skills 的 TDD 证据：说明 skill 不是凭空写出来的，而是针对真实 baseline failure 编写。

### 13.3 为什么强调 standalone

用户明确要求：不要跟 YH Powers 套件混在一起，不要产生任何关联。

因此 skill 不修改任何 YH Powers 文件，也在自身文档中明确 standalone boundary。

### 13.4 为什么不强制完整 packet

完整 packet 对复杂任务有用，但对小任务会制造官僚化流程。

因此 skill 采用 minimal output rule：默认短判断，必要时才用完整结构。

## 14. 验证方式

该 skill 的验证分为三层：

1. **结构验证**：frontmatter、description、目录、standalone boundary 是否正确。
2. **压力场景验证**：同类请求下，agent 是否避免 baseline failure。
3. **独立 review**：由 reviewer 检查是否符合 skill 规范、是否独立、是否可发现、是否可执行。

当前状态：

```text
Skill path: aporia-metaagent/SKILL.md
Skill review: completed before this design doc review
Git commit: not created
YH Powers integration: none
```

## 15. 后续可能方向

仅在用户明确要求时，才考虑后续扩展：

- 将 `aporia-metaagent` 安装到个人 skills 目录。
- 增加英文版 skill。
- 增加更多 pressure scenarios。
- 仅在用户明确要求单独集成时，才建立外部参考；当前 skill 不引用任何项目内 artifact。
- 为特定 agent 平台生成 system/developer prompt 版本。

这些都不属于当前版本。

## 16. 总结

`aporia-metaagent` 是一个独立的通用元认知 skill。

它的核心价值不是让 agent 更“全能”，而是让 agent 在面对模糊、高抽象、多 agent 或容易走偏的任务时，先保护问题定义、边界、方法和验证标准。

它的最重要结论是：

> 真正好的 metaagent 不是全知全能的神，而是知道如何限制自己、如何面对未知、如何防止错误问题被高效解决的守门人。
