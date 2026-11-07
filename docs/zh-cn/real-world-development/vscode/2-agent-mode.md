---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并运行测试。开发者往往需要查找相关文件、进行更改、运行测试、调试、发现遗漏的文件并再次修改，工作环节繁多。

Copilot 智能体模式可以帮助处理这些工作。

Copilot 智能体模式在 IDE 中自主工作：探索项目结构、执行必要更新、运行测试等任务，并自动修复发现的问题。下面了解如何使用智能体模式为网站添加功能。

> [!NOTE]
> 智能体模式和 cloud agent 名称相似，但适用于不同的工作方式。智能体模式在 IDE 中执行任务，适合快速反馈和交互；cloud agent 则像团队成员一样异步工作，通过议题和拉取请求与你协作。

本练习将介绍如何：

- 让 Copilot 智能体模式探索项目、查找相关文件并协同修改。
- 使用 Copilot 智能体模式跨 UI 和数据层实现新功能。
- 在合并到代码库之前审查 Copilot 智能体模式生成的更改和测试。

## 场景

游戏列表不断增长，你希望用户能够按类别和发行商筛选。上一个练习中已添加发行商辅助函数，现在将使用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改代码前，先探索 Tailspin Toys 网站，了解其现有功能。

该网站是一个以开发者为主题的桌游众筹平台，允许用户浏览游戏列表和详情。它是一个 Astro 应用，会在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库，没有单独的后端 API 或客户端 UI 框架。可复用的数据访问辅助函数放在 `src/lib/` 中；交互功能使用标准 DOM API 编写小型、限定作用域的 Astro `<script>`。

### 启动网站

项目提供了 `npm run dev` 命令，用于启动单个 Astro 开发服务器。按以下步骤在 GitHub Codespace 中运行：

1. 返回 Codespace。上个练习结束后应已回到 `main`。
2. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）打开终端。
3. 更新 `main` 并为筛选功能创建分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下内容：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出对话框，询问是否要打开 `http://localhost:4321` 的浏览器窗口，请选择 **x** 将其关闭。

5. 在按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux）的同时，选择终端中的 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 codespace 中选择终端的 localhost 链接时，会自动转到 `https://<your-codespace-name>-4321.app.github.dev/`。这是连接到你所运行开发服务器的私有隧道。

### 探索网站

网站启动后，可以探索它的主要功能：

- **主页**：显示桌游列表，包括标题、图片和描述。
- **游戏详情页**：选择游戏后会打开详情页，显示标题、描述、发行商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始干净的对话。模式和模型选择因练习而异；每个相关步骤都会说明要求。

网站初始版本可以正常运行，但我们希望增加功能。先查看待办事项。使用模板设置项目时，系统已自动创建一组议题；让 GitHub Copilot 显示这些议题。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。Agent 可自主规划并跨文件实现更改、运行终端命令并调用工具。

   ![Chat 视图中智能体选择器的屏幕截图。](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本工作坊作者并未偏好某一个模型。构建本工作坊时使用了 Claude Sonnet 4.5，因此将它写入操作说明，希望代码建议能相对一致。不过，LLM 具有概率性，你可能会看到不同的建议。这完全正常。

> [!NOTE]
> Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，询问待办事项：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 查看生成的议题列表。

注意 Copilot 还根据对用户的帮助程度为这些项目排了优先级。

## 查看说明文件

让智能体生成代码前，先查看将用于提供上下文的说明文件。项目的[用户界面（UI）说明](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)提供了网站功能开发的相关背景。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，包括：
   - 应用架构概述。
   - 组件设计、可测试性和无障碍原则。
   - Astro、Tailwind CSS 和 Drizzle 等文件类型的专项说明链接。

> [!TIP]
> 说明文件既可以引用其他说明文件，也可以引用项目文件。路径相对于说明文件所在位置，因此可以复用内容、拆分较大的说明集，并提供示例和模板。

## 实现筛选功能

要完成筛选功能，至少需要对应用进行以下三项更新：

- 在数据层（`src/lib/`）添加或完善筛选逻辑。
- 添加或更新筛选行为的测试。
- 更新游戏列表页，添加筛选 UI。

合并前还需要运行并通过测试。Copilot 智能体模式可以帮你完成这些任务。

1. 可以继续当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions**，然后选择 **ui** 说明文件。

   ![选择 UI 说明文件的示例](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表中仍然选中 **Agent**。
4. 确认当前模型仍为 **Claude Sonnet 4.5**。
5. 使用与待办议题相关的以下提示，让 Copilot 实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目并查找相关文件，包括数据层辅助函数、UI 和测试；然后修改文件并运行测试。

   ![Copilot 正在探索项目文件的屏幕截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多个任务，例如探索项目、修改文件和运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。工作期间，编辑器中可能会显示 **Keep** 和 **Undo**。完成后，每项更改都会出现 **Keep** 或 **Undo**；工作进行中无需立即选择。

7. Copilot 提示运行测试时，选择 **Continue**。

   ![Copilot Chat 面板中提示用户确认运行测试的对话框](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，甚至出现测试失败。这没有关系。Copilot 会在生成代码和运行测试之间反复迭代，直到任务完成且未发现错误。

   ![Copilot 智能体模式完整 Chat 会话的屏幕截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码是否存在潜在问题。

> [!CAUTION]
> 务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行网站的浏览器并探索新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

恭喜！本练习使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加了功能。你了解了如何：

- 使用 Copilot 智能体模式跨 UI 和数据层实现新功能。
- 让 Copilot 智能体模式探索项目、查找相关文件并协同修改。
- 在合并到代码库之前审查 Copilot 智能体模式生成的更改和测试。

接下来，使用 [Playwright MCP 服务器测试功能][next-lesson]并创建 PR。

### 额外探索练习——实现分页

随着游戏列表不断增长，网站需要支持分页。运用本练习中学到的技能，提示 Copilot 为网站添加分页。可以考虑：

- 遵循现有最佳实践，包括使用已有说明文件。
- 决定由用户选择每页大小，还是使用固定大小。
- 编写提示时，为 Copilot 提供所需指导。
- 可能需要与 Copilot 多次交互，补充上下文并要求修改。这是正常的协作流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
