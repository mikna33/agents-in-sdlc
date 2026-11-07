---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

Copilot 处于智能体模式时，会自主探索代码库、提出更改、编辑文件并运行命令。由于这些工作会在本机实时进行，VS Code 中的 Copilot Chat 会显示每次工具调用和文件编辑。你可以在编辑器中查看每个差异、接受或拒绝单项更改，并通过后续提示引导对话、完善或扩展工作。

本练习将介绍如何：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行中引导智能体，除高对比度模式外再添加浅色模式开关。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行时，设计团队提出了额外需求：为觉得默认主题太暗的用户添加浅色模式开关。无需从头开始新对话，你可以引导当前会话扩展工作。

## 监控 Copilot 进度

Copilot Chat 视图会显示智能体执行的每个步骤，包括探索的文件、运行的命令和建议的编辑。每项文件编辑都会以内联方式显示差异及 **Keep** / **Undo** 控件。

1. 返回练习 4 中使用的 Copilot Chat 视图。
2. 智能体工作时，滚动聊天记录并查看每次工具调用。你会看到文件读取、编辑和请求运行的终端命令。
3. 智能体提出文件编辑时，差异会以内联方式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 撤销更改，让智能体再次尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看该会话修改的所有文件。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。只批准你信任的命令。

5. 等待智能体完成高对比度模式实现。完成时会看到总结更改内容的消息。

## 引导智能体添加浅色模式

高对比度模式完成后，可以在同一对话中扩展功能，添加浅色模式开关。因为继续使用同一聊天会话，智能体保留了之前工作的上下文，包括已修改的组件、样式方案和本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会检查现有更改，并提出浅色模式的修改。
3. 与高对比度模式一样，逐项审查新编辑，并使用 **Keep** 或 **Undo**。
4. 如果智能体的方向不符合预期，例如试图重构切换 UI，可以选择聊天输入框旁的 **Stop** 按钮停止任务，然后补充说明。

> [!TIP]
> 在进行中的会话里引导智能体，比重新开始更快。可以用后续提示提出调整，例如“简化 CSS，使用 CSS 变量而不是重复选择器”、“不要添加第三个开关，把浅色模式集成到现有设置面板”或“同时为两个开关添加键盘快捷键”。

## 审查工作集

提交之前，快速检查智能体修改的所有内容：

1. 在 VS Code 中打开 **Source Control** 视图。
2. 查看已更改文件的完整列表。你应看到 Astro 组件、样式和相关测试的更新。
3. 打开若干更改文件并查看差异。确认其中体现了自定义智能体的无障碍实践，例如 ARIA 属性、键盘导航、语义化 HTML 和本地存储持久化。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。下一课会发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 智能体模式监控并引导进行中的会话，审查每项文件编辑、接受或拒绝更改，并通过后续提示扩展任务而不丢失上下文。

本课程探索了以下概念：

- 实时监控智能体工作。
- 接受或拒绝单项文件编辑。
- 使用后续提示引导进行中的会话。

接下来，在[下一课][next-lesson]中发布无障碍功能，并审查本实验中本地自定义智能体生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
