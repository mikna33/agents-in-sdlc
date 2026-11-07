---
title: "课程 10 - 总结与后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用的自定义项和更多资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你通过 GitHub Copilot CLI 完成了 Tailspin Toys 的连续开发工作流。你：

- 准备 Codespace、安装 Copilot CLI、探索项目并找到预置的筛选议题。
- 添加星级评分，在转发的浏览器中检查结果，并手动合并第一个拉取请求（PR）。
- 从筛选议题开始，在 Plan 模式下确定方案，在 Autopilot 模式下实现，并在 Interactive 模式下审查。
- 使用自定义说明指导智能体，自定义现有 `quality-checks` 技能，并用它运行单元测试、lint 和类型检查。
- 添加 Playwright Model Context Protocol（MCP）服务器，并用它在真实浏览器中探索筛选功能。
- 创建并选择质量保证（QA）自定义智能体，评估需求、覆盖率、技能结果和浏览器证据。
- 审查完整的筛选更改，并授权 Agent Merge 合并筛选 PR。
- 探索用于管理上下文、模型、共享会话以及选择性委派给云智能体的斜杠命令。

## 完成的工作

本工作坊包含两个 PR 里程碑，每个里程碑都从更新后的 `main` 分支创建独立分支：

1. **星级评分**：在游戏卡片上显示现有的 `starRating` 和明确的未评分状态。
2. **筛选和质量工作流**：实现筛选、更新说明并将其应用到功能、自定义 `quality-checks` 报告、创建 QA 配置并添加相关测试。

从规划筛选功能到创建其 PR，你一直使用同一对话和分支。为简化本工作坊，我们将这些工作放在同一个 PR 中。

## 不同类型的验证

你通过多种方式检查代码：自动化测试、亲自在浏览器中检查，以及通过 MCP 让 Copilot 在浏览器中探索。`quality-checks` 技能运行单元测试、lint 和类型检查，并按新格式报告结果。QA 会将这些结果与需求和测试覆盖率审查结合起来，为 PR 做好准备。

新增测试应解决真实缺口；如果 QA 检查后认为不需要新增测试，也可能是正确结果。授权合并前，请审查代码和证据；更改之后也应更新受影响的验证证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。在本工作坊中，你更新了说明、自定义技能、创建 QA 配置并配置 MCP 服务器。可在多个对话中复用这些自定义项，并根据团队需求调整。说明设定规范，技能描述可重复执行的任务，自定义智能体定义专门角色，而 MCP 服务器则连接外部工具。请审查实际更改和工具结果，而不只是智能体的摘要。

为任务选择合适的**模式和模型**。使用 **Plan** 先考虑方案，使用 **Interactive** 参与专注于特定任务的更改，使用 **Autopilot** 处理范围明确的任务。常规编辑可选择更快的模型，复杂工作则可选择能力更强的模型。

上下文仍与基础设施同样重要。清晰说明要构建**什么**、**为什么**以及**如何**构建，会显著影响结果。

## 深入探索

核心工作流已完成。还可以探索以下 CLI 功能：

- `/review`：让代码审查智能体分析更改。
- `/rubber-duck`：讨论问题并获得另一种观点。
- `/fleet`：并行协调彼此独立的子任务。
- `/worktree`：隔离单独的任务。
- `/delegate`：将任务发送给 Copilot cloud agent。

## 后续步骤

提升工具使用能力的最佳方法就是继续使用它。将它用于生产代码、兴趣项目，或者你多年来一直想构建的小应用。与团队分享经验，也向团队成员学习。也别忘了继续探索文档。

如果想通过终端扩展 Tailspin Toys，可继续学习可选的 [Foundry Backer Concierge 系列][foundry]。若想比较其他环境，可以探索 [VS Code 工作坊][vscode]、[GitHub Copilot app 工作坊][app]或 [Copilot cloud agent 工作坊][cloud]。

还可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给 cloud agent。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
