# ECC 命令何时使用

这是一份面向 `commands/` 的中文速查表，覆盖仓库里当前的 80 个斜杠命令。

先记住一条总原则：ECC 现在是 `skills-first`。很多命令只是兼容旧习惯的入口；如果某条写着“旧入口”，表示它还能用，但长期建议直接用对应 skill。

## 快速选择

| 你现在想做什么 | 优先用 |
|---|---|
| 想用一个入口跑完整的功能开发闭环 | `/feature-workflow` |
| 先把需求讲清楚，再决定怎么做 | `/plan` |
| 先做产品说明，再出实施方案 | `/prp-prd` → `/prp-plan` |
| 严格按测试先行开发 | `/tdd` |
| 构建、类型检查、编译挂了 | `/build-fix` 或对应 `*-build` |
| 审本地改动 | `/code-review` |
| 深审 GitHub PR | `/review-pr` |
| 补测试覆盖率到 80%+ | `/test-coverage` |
| 跑 E2E / Playwright | `/e2e` |
| 查某个库的最新官方文档 | `/docs` |
| 保存或恢复长会话 | `/save-session` / `/resume-session` |

## 1. 规划与交付

| 命令 | 什么时候用 |
|---|---|
| `/agent-sort` | 想按当前仓库实际需求筛选 ECC 能力，只保留真正常用的 DAILY / LIBRARY 面。旧入口。 |
| `/feature-workflow` | 想从需求澄清、上下文补齐、规划、TDD、审查到验证都走一条完整闭环，并在里程碑保存 handoff / pitfalls。 |
| `/feature-dev` | 想按“先读代码、再问问题、再设计、再实现”的完整流程开发功能。 |
| `/plan` | 需求复杂、改动面大、还不该立刻写代码时。 |
| `/tdd` | 想严格按 RED → GREEN → REFACTOR 做开发时。旧入口。 |
| `/build-fix` | 项目能跑到编译/构建阶段，但报错很多，需要通用型逐个修复。 |
| `/model-route` | 不确定该用哪档模型处理当前任务，想先做模型选型。 |
| `/prp-plan` | 需要一份非常完整的实施计划，连代码模式、依赖、风险、验证命令都先写清楚。 |
| `/prp-implement` | 已经有 PRP 计划文件，准备按计划执行并在每步后验证。 |
| `/prp-prd` | 还在产品想法阶段，想通过问答先形成 PRD，再进入开发。 |
| `/prp-commit` | 想用自然语言描述“把哪些内容提交掉”，快速生成一次提交。 |
| `/prp-pr` | 当前分支已经有改动或未推送提交，想直接创建 GitHub PR。 |

## 2. 审查、验证与质量

| 命令 | 什么时候用 |
|---|---|
| `/code-review` | 想审本地未提交改动，或快速审一个 PR。 |
| `/review-pr` | 想对 GitHub PR 做更重、更系统的多视角审查。 |
| `/quality-gate` | 想手动跑一遍格式化、lint、type check 等质量门，而不是等 hook。 |
| `/verify` | 想按仓库上下文跑一整套验证流程：build、types、lint、tests、diff 检查。旧入口。 |
| `/checkpoint` | 大任务中想留一个可对比的里程碑，或回头验证某个阶段是否退化。 |
| `/eval` | 想建立、运行、汇报或维护一个评测集/回归集。旧入口。 |
| `/e2e` | 想生成或更新 Playwright E2E，用真实用户路径验证功能。旧入口。 |
| `/test-coverage` | 想找出覆盖率薄弱点，并补测试把整体拉到 80%+。 |
| `/refactor-clean` | 想安全删除死代码、无用依赖和重复包装，并且每步都跑测试。 |
| `/harness-audit` | 想给仓库当前的 ECC/harness 配置做打分审计，并拿到优先整改建议。 |
| `/santa-loop` | 风险很高，想让两个独立审查模型都通过后再认为代码可交付。 |

## 3. 语言专项

| 命令 | 什么时候用 |
|---|---|
| `/cpp-build` | C++ / CMake / 链接错误需要逐步修复时。 |
| `/cpp-review` | 写完或评审 C++ 代码，重点看内存安全、并发和现代 C++ 用法。 |
| `/cpp-test` | C++ 代码要按 GoogleTest + 覆盖率走 TDD 时。 |
| `/flutter-build` | Flutter / Dart 构建、依赖解析、代码生成或 analyzer 报错时。 |
| `/flutter-review` | Flutter / Dart 代码需要审查 widget、状态管理、性能和可访问性时。 |
| `/flutter-test` | Flutter / Dart 测试挂了，或你想补 unit/widget/golden/integration 测试时。 |
| `/go-build` | Go 的 `build`、`vet` 或 linter 错误需要修复时。 |
| `/go-review` | 想审 Go 代码的并发安全、错误处理和惯用写法时。 |
| `/go-test` | Go 功能开发要用表驱动测试和覆盖率门槛时。 |
| `/gradle-build` | Android / Kotlin Multiplatform / Gradle 构建链本身出问题时。 |
| `/kotlin-build` | Kotlin / Gradle 编译、依赖或 detekt 报错时。 |
| `/kotlin-review` | 想审 Kotlin 代码的空安全、协程安全和惯用模式时。 |
| `/kotlin-test` | Kotlin 功能开发要走 Kotest + Kover 的 TDD 流程时。 |
| `/python-review` | 想审 Python 代码的类型提示、PEP 8、Pythonic 风格和安全问题时。 |
| `/rust-build` | Rust 的编译、borrow checker、lifetime 或依赖问题卡住时。 |
| `/rust-review` | 想审 Rust 代码的 ownership、unsafe、错误处理和惯用写法时。 |
| `/rust-test` | Rust 功能开发要按测试优先和覆盖率门槛推进时。 |

## 4. 文档、规则与技能

| 命令 | 什么时候用 |
|---|---|
| `/docs` | 需要查某个库、框架或 API 的最新官方文档，而不是凭记忆回答。旧入口。 |
| `/update-docs` | 想把文档和真实代码、脚本、环境变量、运行方式重新同步。 |
| `/update-codemaps` | 想生成精简的架构图谱，方便人和 AI 快速理解代码结构。 |
| `/prompt-optimize` | 只想把提示词写得更好，不想立即执行任务。旧入口。 |
| `/rules-distill` | 规则文档太长，想蒸馏成真正可执行的核心约束。旧入口。 |
| `/skill-create` | 想从本地 Git 历史里提炼模式，生成新的 `SKILL.md`。 |
| `/skill-health` | 想看当前技能体系的覆盖度、健康度和分析图表。 |
| `/context-budget` | 任务太大，想知道上下文都花在哪、哪里最该压缩。旧入口。 |

## 5. 学习与 Instinct

| 命令 | 什么时候用 |
|---|---|
| `/learn` | 刚解决了一个非琐碎问题，想把经验提炼成可复用模式。 |
| `/learn-eval` | 想提炼经验，但先做质量评估，并判断该存全局还是项目级。 |
| `/instinct-status` | 想查看当前项目和全局已经积累了哪些 instincts。 |
| `/instinct-import` | 想把外部 instincts 导入当前项目或全局空间。 |
| `/instinct-export` | 想把已有 instincts 导出，便于迁移、共享或备份。 |
| `/evolve` | 想把积累的 instincts 进一步进化成 skill、command 或 agent。 |
| `/promote` | 某个项目内的 instinct 很通用，想升级成全局复用。 |
| `/projects` | 想查看 ECC 已知项目及其 instinct 统计。 |
| `/prune` | 想清理长期未提升、也没人再用的 pending instincts。 |

## 6. 会话、上下文与辅助

| 命令 | 什么时候用 |
|---|---|
| `/aside` | 主任务进行中，想插问一个旁支问题，又不想打断主线。 |
| `/save-session` | 结束前保存当前上下文，方便以后无损接续。 |
| `/resume-session` | 新开会话后，想把上次保存的上下文完整接回来。 |
| `/sessions` | 想查看、管理会话历史、别名和元数据。 |
| `/claw` | 需要操作或扩展 `scripts/claw.js` / NanoClaw REPL。旧入口。 |

## 7. 循环、多模型与编排

这一组通常比普通命令更重。只有当单会话、单代理或单分支流程明显不够时，再考虑它们。

| 命令 | 什么时候用 |
|---|---|
| `/loop-start` | 想启动一个带安全默认值的自治循环工作流。 |
| `/loop-status` | 想查看循环当前状态、卡点、成本漂移或是否需要人工介入。 |
| `/multi-plan` | 想让多模型先协作分析并产出计划，但还不修改生产代码。 |
| `/multi-execute` | 已经有计划，想让多模型分工协作执行。 |
| `/multi-backend` | 后端任务较重，想用以后端为中心的多模型协作流程。 |
| `/multi-frontend` | 前端 / UI 任务较重，想用以前端为中心的多模型协作流程。 |
| `/multi-workflow` | 需要 `tmux/worktree` 级并行 worker，且每个 worker 要独立 git 状态或终端。 |
| `/orchestrate` | 想把复杂任务交给编排层统筹多个 agent / workflow。旧入口。 |
| `/devfleet` | 想把任务组织成 mission/fleet 形式进行较大规模的协同执行。旧入口。 |
| `/gan-build` | 想用“生成器 + 评估器”多轮迭代，把应用做到达到评分阈值。 |
| `/gan-design` | 想把重点放在前端视觉突破，用 GAN-style 循环逼近更高设计质量。 |

## 8. 运行、集成与环境

| 命令 | 什么时候用 |
|---|---|
| `/hookify` | 想基于对话或显式规则生成 hooks，主动拦截不希望发生的行为。 |
| `/hookify-configure` | 想启用、禁用或微调某条 hookify 规则。 |
| `/hookify-help` | 不清楚 hookify 的概念、用法或配置方式时。 |
| `/hookify-list` | 想查看当前已经配置了哪些 hookify 规则。 |
| `/jira` | 需要读取、分析、更新或评论 Jira 工单时。 |
| `/pm2` | 想扫描项目并自动生成 PM2 配置与多服务启动命令。 |
| `/setup-pm` | 想统一设置当前项目或工作环境的包管理器偏好。 |

## 一个够用的记忆法

1. 不确定先用 `/plan`。
2. 需求还没成型先用 `/prp-prd`，需求已清晰但实现复杂用 `/prp-plan`。
3. 想一条龙跑完整闭环先用 `/feature-workflow`，写功能偏轻量工程流程用 `/feature-dev`，偏严格测试驱动用 `/tdd`。
4. 编译挂了用 `/build-fix` 或对应 `*-build`，质量兜底用 `/verify`。
5. 审代码用 `/code-review`，审 PR 用 `/review-pr`。
6. 查官方资料用 `/docs`，保存上下文用 `/save-session`，恢复上下文用 `/resume-session`。
