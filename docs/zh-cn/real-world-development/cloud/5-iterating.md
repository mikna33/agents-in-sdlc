---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在整个练习中，你让 GitHub Copilot 执行了多项任务，以改进用户体验并添加功能，包括添加代码文档、构建相关游戏功能，以及实现高对比度和浅色模式等无障碍功能。现在查看代码更改；必要时向 Copilot 提供反馈，改进其工作。

### 场景

加入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。我们仍然需要审查生成的代码并遵循正常的 DevOps 流程。现在先审查 Copilot 创建的文档、相关游戏功能和无障碍功能，然后再为团队其他成员启用审查。

## 安全性和 GitHub Copilot cloud agent

Copilot cloud agent 会异步且自主地执行任务，因此设有一些安全限制：

- Copilot 对存储库只有读取权限，对自身工作分支才有写入权限。
- cloud agent 在 GitHub Actions 中运行，并创建独立的临时工作环境。
- 运行任何 GitHub Actions 工作流前，都需要人工批准。
- [默认情况下，外部资源访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，MCP 服务器也包括在内。

## 审查生成的文档

先查看 GitHub Copilot cloud agent 创建的第一个拉取请求（PR）：为代码添加文档。此处将使用 GitHub.com 上的标准 PR 界面。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认只能有限访问外部资源，包括外部 MCP 服务器。你可以[自定义或禁用 Copilot cloud agent 防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开 PR 列表。
3. 打开标题类似于 **Add missing documentation** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 会带有 **[WIP]** 标记。请等待 Copilot 完成，这可能需要几分钟。

4. PR 准备就绪后，选择 **Files changed** 选项卡，审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新添加的 TSDoc 文档注释和其他文档。具体更改可能有所不同。检查其中的 TSDoc 注释、TypeScript 规范和文件注释头；它们来自练习 2 开始时查看的自定义说明文件。
6. 如果审查结果良好，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

9. 工作流会显示在 PR 的检查区域中。如果一切正常，单个 Astro 应用的项目检查就会通过。这可能需要几分钟。

## 向 GitHub Copilot 请求更改

与 Copilot 协作并非单向过程。你可以像对待团队成员一样，在 PR 评论或代码内联评论中提及 Copilot。Copilot 会读取评论并启动新会话来处理请求。由于结果具有非确定性，无法提供唯一固定的提示。可以要求 Copilot：

- 在每个代码文件开头添加简短的文件注释头。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 为 Astro 应用结构创建 README。

1. 添加评论并提及 **@copilot**，请求更新生成的文档。可以使用上述建议，也可以提出其他文档改进请求。
2. 选择 **View Session**，观察 Copilot 如何启动新会话并执行更新。
3. 选择 **Back to pull request** 返回 PR。
4. Copilot 完成更改后，PR 中会出现新的提交。
5. 选择 **Files changed** 选项卡并审查更改。

可以不断迭代，直到满意为止。完成后，可将 PR 从草稿转换为准备审查状态，再合并到主分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

现在查看 Copilot 为“在游戏详情页显示相关游戏”议题创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 如果更改看起来没问题，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有等待批准的工作流。
7. 如果有，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

8. 工作流会显示在 PR 的检查区域中。如果一切正常，单个 Astro 应用的项目检查就会通过。
9. **可选：**可以在 Codespace 中切换到该分支，手动测试相关游戏功能。打开终端并运行以下命令，将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已构建相关游戏功能。和之前一样，可以通过 PR 评论要求 cloud agent 迭代，例如调整显示的相关游戏数量，或补充文件注释头和 TSDoc 注释。注意，此任务分配时你还没有更新自定义说明。请在 **Conversation** 选项卡中添加评论，Copilot 会据此启动新会话。

## 审查无障碍功能

最后，审查由无障碍自定义智能体实现的功能。该 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 中在任务控制中心追加的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 会带有 **[WIP]** 标记。请等待 Copilot 完成，这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现，重点检查：
   - 用于切换模式的 UI 组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 如果更新看起来没问题，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有等待批准的工作流。
8. 如果有，选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)

9. 工作流会显示在 PR 的检查区域中。如果一切正常，单个 Astro 应用的项目检查就会通过。
10. **可选：**可以在 Codespace 中切换到该分支，手动测试无障碍功能。打开终端并运行以下命令，将 `<branch-name>` 替换为 Copilot 创建的分支名称：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式开关，并确认页面重新加载后设置仍然有效。

请注意，无障碍自定义智能体如何帮助 Copilot 根据无障碍最佳实践实现功能。如果发现问题或有改进建议，可以像之前一样在评论中提及 **@copilot** 请求更新。

## 可选练习 — 继续委派工作

cloud agent 适合处理真实待办事项，让你有时间完成其他工作。可以在自己的存储库中创建更多议题并分配给 Copilot，例如：

- 在游戏详情页创建支持者兴趣表单。
- 为游戏列表页添加分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成 Cloud agent 环境的练习。过程中，你：

- **检查了存储库自带的自定义说明文件**，以便之后观察其对 cloud agent 输出的影响。
- **将议题分配给 Copilot cloud agent**，观察它设置环境、制定计划并异步执行任务。
- **创建并使用自定义智能体**实现无障碍功能，添加高对比度和浅色模式切换。
- **使用 Copilot agents 页面作为任务控制中心**，监控并引导无障碍会话。
- **审查并迭代 cloud agent 的拉取请求**，使用 **@copilot** 请求更改并批准工作流。

## 回顾和后续步骤

你已完成 Cloud agent 环境的练习。其他环境可以补充本次实践的内容：

- 🖥️ **[VS Code 环境](../../vscode/)** — 在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 环境](../../cli/)** — 在终端中使用 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接，交付两个经过审查的更改。

还可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给 cloud agent。
- 为其他领域创建自定义智能体。
- 为其他技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot cloud agent 设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot cloud agent 防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
