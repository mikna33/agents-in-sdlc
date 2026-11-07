---
title: "练习 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

许多组织都面临技术债务，例如尚未解决的安全问题、需要更新的旧代码，或长期搁置的功能请求。GitHub Copilot cloud agent 可以自主完成更新代码和添加功能等任务。完成后，它会创建草稿 PR，供开发者审查。这样可以将繁琐任务交给智能体，加快开发流程，让开发者专注于更重要的工作。

本练习将介绍如何使用 cloud agent：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 了解明确界定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初承接网站开发的承包商没有留下文档，因此团队希望先为应用中所有导出的函数添加 TSDoc 注释。

设计团队也希望改进游戏发现体验：每款游戏的详情页应显示同类别的其他游戏，以便用户继续浏览。暂时不需要完善的实现，只需要可用于 UX 验收测试的功能。此项工作目前受阻，优先级低于其他议题。

这类任务很容易被推迟，适合交由 cloud agent 异步处理。开发者可以继续处理其他工作，之后再审查结果。

## 了解 GitHub Copilot cloud agent

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) 可以像人类开发者一样在后台执行任务。可以通过多种方式启动它，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求跟踪进度、设置工作环境并开始处理。你可以在会话进行中或完成后查看详情。待方案准备好供审查时，Copilot 会在拉取请求中通知你。

## 明确界定说明范围的重要性

GitHub Copilot 并非魔法，不能替你跳过思考。即使看似简单的操作，也可能包含复杂细节。因此，应仔细界定交给 cloud agent 的任务范围，并像对待 AI 结对程序员一样，分阶段工作、学习、试验并调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 本存储库中的自定义说明

前面的练习介绍了自定义说明及其作用。如果你完成过自定义说明练习，已经见过 `.github/copilot-instructions.md` 的效果；如果还没有，可以趁此快速查看。将工作分配给 cloud agent 前，先以只读方式查看存储库中的说明文件，之后便能识别它们的作用。

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 规范和 TSDoc 注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它将说明限定于 `**/*.test.ts` 文件。

将 **Code lacks documentation** 议题分配给 cloud agent 时，注意 PR 中的 TSDoc 注释、TypeScript 规范和文件注释头；它们来自这些说明文件。之后的练习会再次带你审查这些 PR。

## 为 Copilot cloud agent 设置开发环境

无论由谁编写代码，通常都需要配置环境并运行设置脚本。将任务分配给 Copilot 时也一样。cloud agent 使用 [GitHub Actions][github-actions] 执行工作，可以通过特殊的[设置工作流][setup-workflow]自定义环境。工作流配置在 `.github/workflows/copilot-setup-steps.yml` 中，会在 agent 开始工作前运行，以安装所需工具和依赖项。

本练习已预先配置该工作流，确保 Copilot 可以使用 Node.js、项目依赖项、端到端测试所需的浏览器，以及已迁移和填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的“Actions”选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 为这些步骤设置所需的最低权限。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果要在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果未在设置步骤中克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试时有可用数据。
      - name: Set up the database
        run: npm run db:setup
```

该文件与其他 GitHub 工作流类似，但有几个要点：

- 其中只有一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理 PR 前，GitHub Actions 会运行此作业。
- `workflow_dispatch` 触发器允许你从存储库的 **Actions** 选项卡手动运行工作流。这有助于测试设置是否成功，而不必等待 Copilot 启动它。

## 添加文档

许多项目的文档过时或缺失，这类技术债务会降低工作效率，也增加维护代码或帮助新开发者熟悉项目的难度。Copilot 擅长生成文档，因此可以将此议题分配给 cloud agent，在后台生成所需文档，稍后再审查其工作。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开刚创建的 PR，标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中尚未出现新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会创建待办列表。

> [!NOTE]
> Copilot 可能需要几分钟才能在 PR 中显示待办列表。它正在创建环境（运行前面介绍的工作流）、分析项目并确定处理问题的方案。

13. 查看 Copilot 即将完成的任务。
14. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到最新状态。

16. 浏览实时会话，观察 Copilot 如何探索代码、了解状态、思考并确定计划，然后编写代码。

整个过程可能需要几分钟。cloud agent 的主要优势之一是能异步处理任务，让你继续做其他工作。接下来会再分配一个任务给 cloud agent，同时继续编写应用功能。

## 为游戏详情页添加相关游戏区域

GitHub Copilot cloud agent 的优势之一是可以分工：你处理一项任务时，智能体同时处理另一项任务。我们把添加相关游戏区域的任务也分配给 cloud agent。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。
9. 选择 **Assign**。

稍后你会在议题的第一条评论中看到 👀，表示 Copilot 已开始处理。

Copilot 正在后台处理第二项请求。cloud agent 的工作方式类似软件工程师，因此无需一直监控；完成后再审查即可。现在可以转向创建和使用自定义智能体。

## 总结和后续步骤

本课程介绍了 [GitHub Copilot cloud agent][copilot-agents]。你可以将议题分配给 Copilot，让它异步处理技术债务、创建新功能，或协助将代码迁移到其他框架。

本课程探索了以下概念：

- 自定义代码生成环境。
- 安全地执行操作。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

cloud agent 正在后台工作。接下来转向创建和使用自定义智能体。[Copilot cloud agent 也可以使用 MCP 服务器][cloud-agent-mcp]，并能读取前面模块介绍的自定义说明。

## 资源

- [关于 Copilot cloud agent][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot cloud agent 设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
