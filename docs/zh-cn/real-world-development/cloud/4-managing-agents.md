---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几个练习中，你让 Copilot cloud agent 承担了三项不同任务，用于改进用户体验和添加功能。虽然 cloud agent 可以异步自主工作，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给 cloud agent 的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心中，可以查看所有带有未关闭 PR 的智能体任务、查看它们执行的操作，并引导进行中的会话。

本课程将介绍如何：

- 使用智能体页面监控 cloud agent 任务。
- 引导进行中的会话，让智能体添加新功能。

## 场景

将任务分配给智能体以创建高对比度模式后，团队意识到也应添加浅色模式。既然正在更新网站样式并添加切换功能，就可以把这一功能一起实现。你将引导智能体，同时添加浅色模式和高对比度模式。

## 查看 Copilot cloud agent 任务

查看当前分配给 Copilot cloud agent 的任务状态：

1. 打开 [Copilot agents 页面](https://github.com/copilot/agents)。
2. 查看主区域和左侧区域中的任务列表。列表中应包括你分配给 Copilot 的任务，例如：
   - 为代码库补充文档。
   - 为游戏修改功能生成 API。
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的工作，例如：
   - 从存储库检出代码。
   - 为 Copilot 创建工作环境。
   - 设置 MCP 服务器。
   - 执行完成任务所需的其他步骤。

> [!NOTE]
> 列出的具体步骤会根据 Copilot 的工作状态和所采用的方法而变化。

4. 同时查看右侧的拉取请求区域，了解 PR 和已更改文件，以便进一步监控。

## 引导 cloud agent

现在你已查看正在进行的任务，可以让负责高对比度模式的 Copilot 同时添加浅色模式。

1. 选择与添加高对比度模式相关的会话。确切标题因 Copilot 使用的名称和当前工作状态而异。

    ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到 Copilot 完成设置并开始工作。此时会看到类似的消息。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面中的 cloud agent 任务及突出显示的引导活动会话对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 会确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务。收到消息时，它会先完成当前步骤，再将新请求纳入工作流程。

这同样需要几分钟。等待时，可以停下来回顾目前学到和探索的内容。

## 总结和后续步骤

本课程介绍了 Copilot agents 页面，它是监控和引导 GitHub Copilot cloud agent 任务的中心位置。通过任务控制中心，可以跟踪所有活动和已完成的任务、审查正在进行的工作，并调整进行中任务的方向。

本课程探索了以下概念：

- 使用任务控制中心和智能体页面监控 cloud agent 任务。
- 引导进行中的会话，让智能体添加新功能。

Copilot 正在完成无障碍功能。接下来转向[迭代 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot agents 页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
