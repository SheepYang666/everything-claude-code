# Codex 下使用 ECC 的开发场景手册

这不是安装文档。这是一份给 **已经在 Codex 里装好 Everything Claude Code（ECC）** 的人的实战手册。

你会拿到两样东西：
- 一套 **Codex 可直接粘贴** 的完整提示词
- 一套按真实工作流组织的场景示例，而不是散乱的命令列表

如果你还不熟悉 ECC 里有哪些 command / workflow / skill，先看 [ECC 命令何时使用](./COMMANDS-WHEN-TO-USE.md)。

## 先记住 4 件事

### 1. 在 Codex 里，ECC 不是先输入 `/skill`

Codex 下的 ECC 主要靠三层工作：
- `AGENTS.md` 提供全局行为约束
- `.agents/skills/` 里的 skills 自动加载
- ECC 的传统 `/plan`、`/tdd`、`/code-review` 等能力，在 Codex 里改成 **instruction-based** 调用

也就是说，你通常不会先输入：

```text
/skill tdd-workflow
```

而是直接对 Codex 说：

```text
Use the `tdd-workflow` skill to implement this feature.
```

或者：

```text
Use the ECC `code-review` workflow on my current changes.
```

### 2. 把 skill / workflow 名字写进提示词里

在 Codex 里，最稳的做法是直接点名：
- 想用 skill，就写 `Use the \`xxx\` skill`
- 想用 ECC 命令式工作流，就写 `Use the ECC \`xxx\` workflow`
- 想用 Codex 原生命令，就直接用 `/review`、`/agent`

### 3. 一个好提示词至少包含 5 件事

```text
Use the `tdd-workflow` skill.

目标：
当前状态：
约束：
你必须输出什么：
完成前必须做的验证：
```

### 4. 大多数开发任务都能套进这个闭环

```text
设计 / 拆解：Use the ECC `plan` workflow
实现 / 修复：Use the `tdd-workflow` skill
审查：Use the ECC `code-review` workflow
收尾验证：Use the `verification-loop` skill
```

如果你不知道从哪里开始，就从 `plan` 开始。

## 一个通用模板

先给你一条最常用的通用模板。后面的场景基本都只是这个模板的具体化。

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `plan` workflow first.
Then choose the right ECC skill or workflow for implementation.

目标：
[写清你要做的事]

当前状态：
[写清当前仓库、已有实现、报错或限制]

约束：
- Preserve existing behavior unless explicitly changing it
- Follow existing project conventions
- Prefer small, verifiable steps

你必须输出：
1. 你准备调用的 ECC workflow / skill
2. 执行顺序
3. 关键产出物
4. 完成前的验证步骤
```

如果你不想每次都手动写 `plan -> tdd-workflow -> code-review -> verification-loop` 这一串，现在也可以直接让 Codex 走一个统一入口：

```text
Use the `feature-workflow` skill.
Communicate in Chinese.

Feature request:
[写需求]

Current context:
[写上下文]

Constraints:
[写约束]
```

这个 skill 会先补齐缺失信息，再规划、测试先行实现、审查、验证，并在里程碑提示你沉淀 handoff / pitfalls。

---

## 场景 1：Web 前端设计

**什么时候用：** 你还没准备开始写代码，先要把页面结构、视觉方向、关键交互和组件层次定下来。

**推荐能力：**
- ECC `plan` workflow
- `frontend-patterns` skill
- 如果你的 Codex 安装同步了完整技能集，可额外点名 `frontend-design`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `plan` workflow.
Use the `frontend-patterns` skill.
If `frontend-design` is available in this install, use it as well.

我需要设计一个 B2B SaaS 管理后台首页，服务对象是团队管理员。页面需要同时展示：
- 今日核心指标
- 最近异常告警
- 待处理任务
- 团队活跃度趋势

先不要写代码。

你必须输出：
1. 页面目标和主要用户任务
2. 信息架构
3. 页面区块结构
4. 关键交互状态（默认、空状态、加载、错误）
5. 组件拆分建议
6. 视觉方向建议（字体、颜色、密度、布局风格）
7. 哪些地方最容易做成“看起来像模板站”

约束：
- 桌面端优先，但要兼顾移动端
- 不要给泛泛的 UI 建议，要给可实现的结构
- 说明哪些部分应该在实现阶段先做

最后单独列出“确认后再进入开发”的事项。
```

**这条提示词会调动什么：**
- `plan` 帮你先做结构化拆解
- `frontend-patterns` 帮你把设计转成组件和状态设计
- `frontend-design` 如果可用，会把视觉方向拉得更具体

**预期产出物：**
- 一份页面蓝图
- 一个组件树
- 一份设计约束清单
- 一个“先做什么、后做什么”的前端实施顺序

**结束前验收：**
- 是否明确了桌面和移动端布局思路
- 是否覆盖了空状态 / 错误态 / 加载态
- 是否已经拆到可以进入实现阶段

---

## 场景 2：Web 前端开发

**什么时候用：** 设计已经定了，准备落代码、补测试并验证关键交互。

**推荐能力：**
- `tdd-workflow`
- `frontend-patterns`
- `e2e-testing`
- `verification-loop`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the `tdd-workflow` skill.
Use the `frontend-patterns` skill.
After implementation, use the `e2e-testing` and `verification-loop` skills.

我需要把一个“团队邀请”页面实现出来。页面需求：
- 输入邮箱
- 选择角色
- 发送邀请
- 展示当前待接受邀请列表
- 支持撤销邀请

请按这个顺序工作：
1. 先识别现有前端模式和组件约定
2. 写测试计划
3. 先补单元 / 集成测试，再实现
4. 为关键流程补 E2E
5. 最后做验证总结

约束：
- 不要绕开测试直接写实现
- 保持现有设计系统和项目风格
- 明确指出哪些测试是组件级，哪些是端到端
- 如果需要后端接口假设，请先列出来，不要悄悄假设

最终请输出：
1. 测试先行的实现顺序
2. 关键组件和状态
3. 关键测试用例
4. 完成后的验证命令
```

**预期产出物：**
- 可执行的实现路线
- 前端测试清单
- 关键页面状态说明
- E2E 覆盖范围

**结束前验收：**
- 是否先给了测试再给实现
- 是否说明了哪些交互必须做 E2E
- 是否明确了最终验证步骤

---

## 场景 3：算法开发

**什么时候用：** 你要实现核心算法、评分器、调度器、匹配器、搜索策略或数据处理逻辑。

**推荐能力：**
- ECC `plan` workflow
- `tdd-workflow`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `plan` workflow first.
Then use the `tdd-workflow` skill.

我要实现一个“任务优先级评分器”，输入包括：
- 截止时间
- 业务价值
- 风险等级
- 预计耗时
- 依赖数量

输出是一个可排序的分数和分数解释。

你必须按这个顺序输出：
1. 数据结构设计
2. 评分公式候选方案（至少 2 个）
3. 选择其中一个方案并解释理由
4. 时间复杂度和空间复杂度
5. 边界条件
6. 测试样例设计
7. 测试先行的实现顺序

约束：
- 不要一开始就写代码
- 明确哪些是业务规则，哪些是算法约束
- 如果评分结果可能不稳定，要指出原因
- 给出最小可行版本和后续可优化点
```

**预期产出物：**
- 算法方案
- 复杂度分析
- 边界条件列表
- 可以直接进入实现的测试计划

**结束前验收：**
- 是否区分了“规则设计”和“代码实现”
- 是否给出了可验证的样例
- 是否指出了未来优化空间

---

## 场景 4：测试补齐与覆盖率提升

**什么时候用：** 功能已经有了，但测试不足、覆盖率低、关键路径没人兜底。

**推荐能力：**
- `tdd-workflow`
- `e2e-testing`
- ECC `test-coverage` workflow
- `verification-loop`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `test-coverage` workflow.
Use the `tdd-workflow` skill.
If there are user-facing flows, also use the `e2e-testing` skill.
Finish with the `verification-loop` skill.

我想补齐当前项目的测试和覆盖率，目标是：
- 找出覆盖率最低的模块
- 区分单元测试、集成测试、E2E 的缺口
- 优先覆盖高风险路径

请不要直接开始写所有测试。

你必须先输出：
1. 覆盖率短板清单
2. 哪些模块最值得先补
3. 建议的测试层次划分
4. 每一类测试应该覆盖什么
5. 一份分批推进计划

然后再给出：
6. 第一批最值得补的测试用例
7. 最终如何验证覆盖率是否真的提升
```

**预期产出物：**
- 覆盖率缺口分析
- 按优先级排序的补测计划
- 单测 / 集成 / E2E 分层方案

**结束前验收：**
- 是否避免了“为了数字写低价值测试”
- 是否把高风险路径排在前面
- 是否说明如何复跑覆盖率

---

## 场景 5：Bug 修复

**什么时候用：** 你已经知道哪里不对，但还没最小复现、根因还不够清楚，或者怕修复引入回归。

**推荐能力：**
- `tdd-workflow`
- 如果是编译 / 类型错误，再加 ECC `build-fix` workflow
- `verification-loop`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the `tdd-workflow` skill.
If the bug includes build or type errors, also use the ECC `build-fix` workflow.
Finish with the `verification-loop` skill.

我要修一个 bug：
[把现象、报错、出现频率、触发条件贴在这里]

请严格按这个顺序工作：
1. 先总结 bug 现象
2. 提出最小复现方式
3. 列出最可能的 2-3 个根因
4. 先写失败测试或最小复现检查
5. 再给修复方案
6. 最后给回归验证清单

约束：
- 不要跳过复现直接修
- 如果现有信息不足以稳定复现，要明确缺什么
- 如果你认为这是多个 bug，不要混成一个问题
- 修复后要说明哪些地方最可能回归
```

**预期产出物：**
- 最小复现
- 根因假设
- 失败测试
- 修复方案
- 回归清单

**结束前验收：**
- 是否真的先复现再修
- 是否把回归风险列出来了
- 是否区分了“症状”和“根因”

---

## 场景 6：项目代码审查

**什么时候用：** 你想让 Codex 用 ECC 的风格来审当前改动，重点看 bug、风险、回归和缺测试。

**推荐能力：**
- ECC `code-review` workflow
- 如有安全敏感内容，可点名 `security-review`
- PR 风格审查时可配合 Codex 原生 `/review`

**直接粘贴给 Codex：**

```text
Use the ECC `code-review` workflow on my current changes.
Communicate in Chinese.

Review with findings first.
Focus on:
- correctness bugs
- regressions
- missing tests
- unsafe assumptions
- security-sensitive handling

Output format:
1. Findings first, ordered by severity
2. Each finding must include file + reason
3. Then list open questions
4. Only after that, give a short summary

Do not give style-only suggestions unless they matter to correctness or maintainability.
```

**如果你在做 PR 风格审查：**

先用 Codex 原生命令：

```text
/review
```

然后补一句：

```text
Use ECC code-review standards: findings first, prioritize bugs, regressions, and missing tests.
```

**预期产出物：**
- 按严重级别排序的问题清单
- 对回归风险的提示
- 测试缺口和开放问题

**结束前验收：**
- 是否把 findings 放在最前面
- 是否避免只给“代码风格建议”
- 是否覆盖了测试和安全风险

---

## 场景 7：性能优化

**什么时候用：** 页面慢、接口慢、构建慢、热点函数慢，但你不想一上来就瞎改。

**推荐能力：**
- ECC `plan` workflow
- `verification-loop`
- 需要查框架资料时可加 `documentation-lookup`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `plan` workflow.
If framework-specific behavior matters, use the `documentation-lookup` skill.
Finish with the `verification-loop` skill.

我要做性能优化，问题是：
[描述慢在哪里，例如首页加载 6 秒、某个接口 1.8 秒、某个算法在大输入下变慢]

请先不要提出一堆改法。

你必须先输出：
1. 性能问题的可能瓶颈拆解
2. 最值得先测量的指标
3. 最小优化方案
4. 风险最大的优化方案
5. 推荐先做哪一个，为什么

然后再给：
6. 实施步骤
7. 优化前后应对比的指标
8. 如何验证没有破坏行为
```

**预期产出物：**
- 瓶颈分析
- 指标与测量建议
- 最小优化路径
- 对比验证方案

**结束前验收：**
- 是否先测量后优化
- 是否区分了低风险和高风险改法
- 是否有优化前后的验证指标

---

## 场景 8：重构与清理

**什么时候用：** 代码还能跑，但越来越难改；或者你已经确定要删死代码、拆职责、降复杂度。

**推荐能力：**
- ECC `refactor-clean` workflow
- ECC `code-review` workflow
- `verification-loop`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `refactor-clean` workflow.
After that, use the ECC `code-review` workflow and the `verification-loop` skill.

我要重构这块代码：
[写清模块或路径]

目标：
- 删除死代码
- 降低复杂度
- 保持行为不变

请按这个顺序输出：
1. 先判断这是“清理”还是“结构重组”还是两者都有
2. 列出最安全先动的部分
3. 列出最危险、最容易引起行为变化的部分
4. 拆出一份小步重构顺序
5. 每一步之后该做什么验证

约束：
- 不要把“重构”和“顺手加新功能”混在一起
- 如果某一步无法证明行为不变，就明确标注风险
- 如果可以原子化拆分，请优先原子化
```

**预期产出物：**
- 重构边界
- 小步执行计划
- 每步验证方法

**结束前验收：**
- 是否把清理和功能变更分开
- 是否明确了危险区域
- 是否每步都有验证动作

---

## 场景 9：提交 GitHub

**什么时候用：** 代码改完了，想整理 commit、准备 PR 描述，或者让 Codex 帮你先把交付话术准备好。

**推荐能力：**
- 先用 ECC `code-review` workflow 过一遍
- 然后让 Codex生成 `commit message`、PR 标题和 PR 描述
- 如果你本地有 `gh`，可让 Codex给出命令

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

First use the ECC `code-review` workflow on the current changes.
Then prepare the GitHub delivery artifacts.

我准备把当前改动提交到 GitHub。

你必须输出：
1. 这批改动最合理的 1-2 个 conventional commits
2. 每个 commit 应该包含哪些改动
3. 一个 PR 标题
4. 一个 PR 描述，包含：
   - 背景
   - 做了什么
   - 如何验证
   - 风险与注意事项
5. 如果本地有 `gh`，给我一组可执行命令

约束：
- 不要在没有总结 scope 的情况下直接建议 push
- commit message 要简洁
- PR 描述不要写成流水账
- 如果你认为这批改动应该拆成多个 PR，请直接指出
```

**预期产出物：**
- commit 方案
- PR 标题
- PR 正文
- 可执行命令草案

**结束前验收：**
- 是否先审查再交付
- 是否说明该不该拆 commit / 拆 PR
- 是否给了测试说明

---

## 场景 10：编排文档与项目资料

**什么时候用：** 你已经改了代码，接下来要把变更同步成团队文档、运行手册、架构图谱或对外说明。

**推荐能力：**
- ECC `update-docs` workflow
- ECC `update-codemaps` workflow
- `article-writing`

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `update-docs` workflow.
Use the ECC `update-codemaps` workflow if architecture changed.
Use the `article-writing` skill to polish the final document.

我要把最近的代码变更整理成项目文档。

目标：
- 同步 README / CONTRIBUTING / RUNBOOK / 架构图谱
- 让新同事能快速理解变更
- 避免文档和代码脱节

你必须先输出：
1. 哪些文档最应该更新
2. 哪些信息应该从代码自动提取
3. 哪些信息必须人工撰写
4. 如果架构变了，哪些 codemap 要更新

然后再输出：
5. 推荐的文档更新顺序
6. 每份文档应该新增什么
7. 一版清晰、可交付的文档结构
```

**预期产出物：**
- 文档变更清单
- codemap 更新建议
- 结构化文档草案

**结束前验收：**
- 是否区分了“生成内容”和“人工解释”
- 是否明确了文档优先级
- 是否覆盖了架构变化

---

## 场景 11：搜索调研与完整研究报告

**什么时候用：** 你要评估技术路线、框架选型、竞品、市场、供应商、API 能力，或者研究某个领域的最新进展，而且不想只看 1-2 篇文档就下结论。

**推荐能力：**
- `deep-research`
- `exa-search`
- 如果涉及具体库 / 框架 / API，额外使用 `documentation-lookup`
- 如果研究结果要支持业务判断，再加 `market-research`
- 最终要整理成可交付报告时，可加 `article-writing`

**先说清楚关键原则：**
- 不要只看官方文档，官方文档通常只能回答“它怎么说自己”，不一定回答“真实使用中怎么样”
- 重要结论必须带来源、发布日期和来源类型
- 事实、推断、建议必须分开写
- 如果不同来源互相冲突，要把冲突显式列出来
- 找不到证据时，要明确写“证据不足”，不要补全

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the `deep-research` skill.
Use the `exa-search` skill for broad current-source coverage.
If this topic involves specific libraries, frameworks, APIs, or SDKs, use the `documentation-lookup` skill for official docs and current reference behavior.
If this research will support a business or product decision, also use the `market-research` skill.
After the evidence is collected, use the `article-writing` skill to structure the final report.

我要调研这个主题：
[把主题写在这里]

调研目标：
[比如做技术选型 / 写决策备忘录 / 给老板汇报 / 写投资分析]

受众：
[工程团队 / 产品经理 / 创始人 / 投资人]

请不要只基于少量文档快速总结。

你必须按这个顺序输出：
1. 调研问题拆解：分成 4-6 个子问题
2. 调研覆盖计划：至少覆盖
   - 官方文档 / 官方声明
   - release notes / changelog / 版本变化
   - GitHub repo / issue / discussion / 实际代码信号
   - 第三方深度文章、评测或 benchmark
   - 反向证据或负面案例
3. 对每个子问题分别给出关键发现
4. 每个重要结论都附上：
   - 来源标题
   - 链接
   - 发布时间
   - 来源类型
5. 明确区分：
   - 已确认事实
   - 合理推断
   - 尚不确定的地方
6. 如果来源之间冲突，单独列出冲突点和可能原因
7. 最后整理成一份完整报告，结构必须包含：
   - Executive Summary
   - Methodology
   - Key Findings
   - Trade-offs / Risks
   - Recommendation
   - Unknowns / Gaps
   - Sources

约束：
- 过期信息必须标出来
- 不要把营销文案当成事实
- 不要只给结论，要保留推理链
- 如果证据不够支持强结论，就明确降级成“暂时判断”
```

**预期产出物：**
- 一份有子问题拆解的调研计划
- 一份带来源和日期的完整研究报告
- 一份冲突证据与未知问题清单
- 一份可用于决策的结论与建议

**结束前验收：**
- 是否覆盖了不止一种来源类型
- 关键结论是否都能追溯到具体来源
- 是否单独列出了冲突证据和未知项
- 是否把事实、推断、建议分开
- 是否标记了过期或证据不足的部分

---

## 场景 12：做 PPT / 网页演示

**什么时候用：** 你要做产品演示、技术分享、方案汇报、投资人 deck，最好是网页化 slides。

**推荐能力：**
- `frontend-slides`
- 如果是融资或商业材料，额外用 `investor-materials`

**先说清楚边界：**
- ECC 在 Codex 下最强的是 **网页化演示文稿**
- 如果你最终要 `.pptx`，最好先让 Codex 产出内容结构、每页文案和视觉方向，再决定是否转 PowerPoint

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the `frontend-slides` skill.
If this is investor-facing, also use the `investor-materials` skill.

我要做一份演示文稿，主题是：
[写主题]

受众：
[投资人 / 客户 / 技术团队 / 领导]

目标：
[说服、汇报、演示、教学]

请输出：
1. 整体叙事结构
2. 每一页的标题和核心信息
3. 哪些页应该用图、表、数据、时间线或产品截图
4. 视觉风格建议
5. 如果做成 HTML slides，组件和动画该怎么组织
6. 如果最后还要转成 PPT，哪些信息应该优先保证可迁移

约束：
- 不要写成“每页都是一段大作文”
- 每一页只做一件事
- 如果信息量过大，要主动拆页
```

**预期产出物：**
- 演示结构
- 每页文案
- 视觉与动画建议
- HTML slides 方向

**结束前验收：**
- 是否每页只有一个主目标
- 是否区分了“适合网页化呈现”和“适合 PPT 呈现”的内容
- 是否考虑了受众差异

---

## 场景 13：推导数学公式 / 技术说明

**什么时候用：** 你要把某个算法、损失函数、控制公式、概率模型或优化目标推导清楚。

**先说清楚边界：**
- ECC 不是符号证明系统
- 它更擅长给 Codex 提供 **结构化推导纪律**
- 最终数学正确性仍然应该人工复核

**推荐能力：**
- ECC `plan` workflow
- `article-writing` 用于整理成清晰笔记

**直接粘贴给 Codex：**

```text
You are running inside Codex with Everything Claude Code installed.
Communicate in Chinese.

Use the ECC `plan` workflow first.
After the derivation is complete, use the `article-writing` skill to rewrite the final result into clean technical notes.

我要推导这个公式 / 结论：
[把问题、目标式、背景贴在这里]

请严格按这个顺序输出：
1. 符号表
2. 已知条件
3. 隐含假设
4. 推导目标
5. 逐步推导
6. 每一步为什么成立
7. 最终结果
8. 自检：
   - 量纲是否一致
   - 极限情况是否合理
   - 特殊值代入是否合理

约束：
- 不要一上来就给最终结论
- 如果某一步跳跃太大，要展开
- 如果需要额外假设，请明确标出来
- 最终用适合工程师阅读的中文整理一次
```

**预期产出物：**
- 完整符号表
- 逐步推导
- 自检结果
- 一版可读的技术说明

**结束前验收：**
- 是否先定义符号和假设
- 是否做了量纲 / 极限 / 代入检查
- 是否把最终结果整理成可读版本

---

## 一个真实的日常闭环

如果你每天只想记住一套用法，就记这个。

### 需求刚来

```text
Use the ECC `plan` workflow.
Help me break this feature into implementation steps, risks, interfaces, and validation steps.
Do not write code yet.
```

### 准备实现

```text
Use the `tdd-workflow` skill.
Implement this feature test-first.
List the tests you will add before writing code.
```

### 改完自查

```text
Use the ECC `code-review` workflow on my current changes.
Find bugs, regressions, risky assumptions, and missing tests.
```

### 收尾验证

```text
Use the `verification-loop` skill.
Run the right build, lint, typecheck, tests, and final diff checks for this repo.
Summarize only blockers and final verdict.
```

### 准备提交

```text
Review the current diff and prepare:
1. conventional commit message
2. PR title
3. PR description
4. test plan
```

## 最后一个建议

不要把 ECC 当成一堆命令名。

在 Codex 里，ECC 最有价值的地方不是“你会不会背 `/plan`、`/tdd`、`/code-review` 这些名字”，而是你会不会把它们变成 **稳定的工作流顺序**：

1. 先规划
2. 再测试先行
3. 再审查
4. 最后验证

只要你会把 skill 和 workflow 名字点进提示词里，ECC 在 Codex 下就已经能稳定帮你完成大多数开发工作。
