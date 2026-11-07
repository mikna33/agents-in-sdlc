---
title: "课程 7 - 创建并使用 QA 智能体"
description: "创建一个 QA 自定义智能体，将议题需求、quality-checks 技能和 Playwright MCP 结合起来。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

你已使用 `quality-checks` 技能运行自动检查，也使用 Playwright MCP 在浏览器中查看筛选功能。现在将这些能力整合到具有明确定义 QA 流程的自定义智能体中。

本课程将介绍如何：

- 了解自定义智能体如何结合说明、技能和 MCP 工具。
- 创建并检查可复用的质量保证配置。
- 选择 QA 智能体，并根据筛选议题审查其发现。

## 场景

Tailspin Toys 希望在打开拉取请求（PR）前，一致地审查需求、代码质量、自动检查、测试覆盖率和浏览器行为。自定义智能体可以协调 QA 流程并生成可复用的报告。

## 什么是自定义智能体？

自定义智能体是通过 Markdown 配置文件定义的 Copilot 专用角色。配置文件说明智能体的用途、指令和可用工具。本工作坊会在 `.github/agents/qa.agent.md` 中定义 QA 角色，并在 Copilot CLI 中选择该角色。

不同自定义功能各司其职：存储库说明描述团队规范；`quality-checks` 技能封装可重复运行的检查；Playwright MCP 提供浏览器工具；QA 配置则指导 Copilot 如何利用这些能力评估需求并报告发现。QA 配置不会取代其他功能，也不需要另开对话。

## 创建 QA 配置

打开功能 PR 之前，让 Copilot 创建可复用的 QA 配置。该配置将定义 QA 检查内容及其必须遵循的边界。

1. 返回 Codespace，确认筛选功能对话处于 Interactive 模式。
2. 让默认智能体创建新的自定义智能体：

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## 检查配置

使用新智能体前，先检查其配置，确认 Copilot 捕获了预期的 QA 流程和边界，避免它在你只想验证功能时擅自修改代码。

1. 输入 `/diff` 并打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 为必需项；`name` 是可选项，但添加它能为智能体提供清晰的显示名称。
3. 阅读配置说明，确认 QA 会从需求出发、遵循存储库说明、运行 `quality-checks` 技能并使用 Playwright MCP。
4. 确认 QA 会提供支持证据、修改实现代码前会先询问，而且不会提交更改或打开 PR。
5. 如果生成的配置遗漏了任何职责或边界，请让默认智能体在继续前修订。

## 针对议题运行 QA

Copilot CLI 会在对话开始时加载项目智能体。创建配置后恢复同一筛选功能对话，再选择 QA，这样它就能使用现有议题和规划决定。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 由于刚刚创建了智能体，它可能暂时不会显示在可用智能体列表中。使用上述命令仍可启用它。

2. 使用以下提示，让 QA 智能体审查功能：

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA 智能体开始工作。
4. 完成后，阅读它提供的报告。

## 总结和后续步骤

你添加了可复用的专业角色，并审查了它的工作。本课程中，你：

- 了解了自定义智能体如何结合说明、技能和 MCP 工具。
- 创建并检查了可复用的质量保证配置。
- 选择 QA 智能体，并审查了它根据筛选议题得出的发现。

现在实现、技能更新、QA 配置、测试和验证报告都已准备好接受审查。接下来，将它们整合到功能 PR 中，并使用 [Agent Merge][next-lesson]。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
