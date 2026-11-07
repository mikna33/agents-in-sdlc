---
title: "练习 4 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]是针对开发工作流中特定任务或领域定制的 AI 助手。你可以在存储库的 `.github/agents` 文件夹中通过 Markdown 文件定义智能体，并提供专门的指令、最佳实践、编码模式和领域知识，帮助 Copilot 更有效地完成特定工作。团队可以把专业经验封装成可复用的智能体，例如执行 [WCAG][wcag] 规范的无障碍智能体、遵循安全编码实践的安全智能体，或维护一致测试模式的测试智能体。

自定义智能体由项目 `.github/agents` 文件夹中的 Markdown 文件定义，也可以全局放在 `~/.copilot/agents` 中。每个文件包含 YAML frontmatter，至少需要 `name` 和 `description`，后面是定义智能体行为、专长和指令的 Markdown 提示。

### 自定义智能体与智能体技能的比较

自定义智能体和[智能体技能][agent-skills-concept]在概念上有些重叠。两者主要通过 Markdown 文件定义，都会告诉 AI 如何执行操作。最简单的区分方式是：**自定义智能体是执行工作的角色，技能是可调用的工具。**

自定义智能体拥有自己的上下文窗口，并负责协调技能（甚至其他智能体）完成工作。本实验中，无障碍自定义智能体会根据无障碍指南审查并更新网站；在处理过程中，它可以调用 PR 工作流技能或运行和管理测试的技能。

> [!NOTE]
> 编写自定义智能体没有唯一正确的方法。和 AI 的其他用法一样，应结合实际环境和场景反复测试、迭代。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## 场景

Tailspin Toys 致力于让所有用户都能访问其众筹平台，无论用户的视觉能力或偏好如何。近期用户反馈指出，当前暗色主题的文字与背景对比度不足，难以阅读。为解决这一无障碍问题，设计团队要求实现可由用户开关的高对比度模式。

无障碍访问至关重要，因此希望尽快完成实现。你将使用自定义智能体生成此功能。

本练习将介绍如何：

- 查看现有的无障碍自定义智能体。
- 在 Copilot Chat 中使用无障碍智能体实现高对比度模式。

## 查看无障碍自定义智能体

我们已经为你创建了无障碍自定义智能体。先查看它的内容，了解 Copilot 将如何根据它的指导工作。

返回 Codespace，打开终端并从 `main` 创建无障碍工作分支（与练习 3 中的筛选功能 PR 分开）：

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. 打开 `.github/agents/accessibility.md`。
2. 注意包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> `name` 和 `description` frontmatter 是自定义智能体的必需项。

3. 查看后续部分，了解其中介绍的内容：
   - 生成无障碍网站代码时的核心职责。
   - 无障碍最佳实践。
   - HTML、CSS 和 JavaScript 示例。
   - 常见问题和错误。

## 在 Copilot Chat 中使用自定义智能体

VS Code 会将 `.github/agents` 中定义的所有自定义智能体显示在 Copilot Chat 视图底部的智能体下拉列表中。选择自定义智能体后，聊天会话会遵循其指令和工具配置。

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始干净的对话。模式和模型选择因练习而异；每个相关步骤都会说明要求。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**（如果尚未选中）。

   ![Chat 视图中智能体选择器的屏幕截图。](../../../_images/shared-chat-mode-selector.png)

2. 选择 Chat 视图底部的智能体下拉列表，默认情况下会显示当前智能体 **default**。
3. 从可用智能体列表中选择 **Accessibility agent**。
4. 向无障碍智能体发送以下提示：

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat 会探索代码库、提出更改并应用编辑。每项编辑都会以内联方式显示在聊天中，并包含文件路径和差异供你审查。
6. 随着编辑应用，**Files changed** 指示器会更新，显示此智能体会话已修改的文件。

> [!NOTE]
> 这个过程可能需要几分钟。Copilot 会实际更改存储库文件，包括 Astro 组件、CSS 和相关测试。

下一练习会审查并引导此项进行中的工作。

## 总结和后续步骤

本课程介绍了 [GitHub Copilot 中的自定义智能体][custom-agents]，也就是针对特定任务和领域定制的 AI 助手。自定义智能体可以将团队经验和规范封装成可复用的角色，指导 Copilot 更有效地完成特定工作。

本课程探索了以下概念：

- 自定义智能体的定义方式。
- 在 Copilot Chat 智能体模式中使用自定义智能体。

接下来，[监控并引导智能体工作][next-lesson]，审查实时更改，并在同一会话中添加浅色模式开关。

## 资源

- [关于自定义智能体][custom-agents]
- [在 IDE 中创建自定义智能体][creating-custom-agents-ide]
- [VS Code 中的自定义智能体][custom-agents-vscode]
- [自定义智能体配置][custom-agents-config]
- [awesome-copilot 上的自定义智能体][awesome-copilot-agents]

---

| [← 上一课：使用 Playwright MCP 服务器测试功能][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
