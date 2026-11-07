---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解背景信息，就应确保它能够获取这些上下文。提供特定上下文的方式有多种，其中关键的一种是[说明文件][instruction-files]，它可以为代码生成提供指导。

本练习将介绍如何：

- 了解项目上下文、编码指南和文档规范如何通过存储库自定义说明和按路径限定的说明文件传递给 Copilot。
- 在现有说明的指导下，生成用于筛选功能的第一个数据切片（发行商辅助函数）。
- 向 `.github/copilot-instructions.md` 添加新的全局规范。
- 再次运行相同提示，观察生成的代码如何遵循新规范。
- 将说明更新和筛选功能切片提交到 `main`，供下一练习中的 Copilot 使用。

> [!CAUTION]
> 生成的代码可能不符合部分规范，因为 Copilot 的结果具有非确定性。本练习的重点是观察更新说明后行为的整体变化，而不是要求输出逐字一致。

## 说明文件

### 场景

Tailspin Toys 有一套开发实践指南和要求，包括：

- 数据层必须具有单元测试。
- UI 应采用暗色模式并具有现代风格。
- 应通过 TSDoc 文档注释为代码添加说明。
- 每个文件开头都应添加注释块，说明文件的用途。

你将使用说明文件确保 Copilot 了解这些实践要求并据此完成任务。

### 自定义说明

自定义说明可以为 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。你可以指定编码规范、库以及希望添加的注释类型。说明既可以适用于整个存储库，也可以针对特定文件类型或任务。

说明文件有两种：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送给 Copilot 的单一说明文件。它应包含适用于大多数请求的项目级信息，例如技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：针对特定语言、文件类型或任务提供指南，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，说明文件只用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot cloud agent 都会使用存储库级说明文件及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还支持其他标准的说明文件，例如 AGENTS.md 和 CLAUDE.md。

### 管理说明文件的最佳实践

入门项目中的示例展示了说明文件的常见组织方式：

- 将 `copilot-instructions.md` 聚焦于项目级指导，例如项目内容、结构和全局编码规范。
- 使用 `*.instructions.md` 文件为单元测试、Astro 组件、数据层等文件类型或任务提供专项指导。
- 使用自然语言保持说明清楚，并提供代码的正反示例。

说明文件并没有唯一正确的写法，可以通过试验逐步找到适合项目的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有可靠的说明文件集合。查看本项目的文件时，可以看到适用于多种任务的说明，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮助生成说明文件。不同产品界面的入口不尽相同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。对应环境的课程会在相关步骤说明。
>
> 需要模板或起点？可以查看包含说明文件、自定义智能体和其他资源的 [awesome-copilot][awesome-copilot]。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 查看项目中的自定义说明文件

先查看存储库自带的说明文件，再进行修改，以便观察更改的效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 查看项目简介和编码指南。这些说明适用于本存储库中与 Copilot 的所有交互。
4. 打开 `.github/instructions` 文件夹并查看其中的文件，包括 Astro、Drizzle 数据层、测试等说明。
5. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它使用相对于存储库根目录的 glob 决定适用文件；例如，`**/*.test.ts` 会匹配 TypeScript 测试文件。
6. 查看该文件中针对单元测试的说明。
7. 打开 `.github/instructions/drizzle.instructions.md`，查看它对其他说明文件和项目现有文件的链接。这样可以将较大的说明集拆分成可复用的小文件，并提供可参考的示例。（其中的路径相对于说明文件所在位置，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目的编码规范，但尚未要求在代码中编写文档。接下来会添加 TSDoc 注释和文件注释头规范。

## 创建分支

为本次更改创建分支：

1. 返回上一个练习中的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始干净的对话。模式和模型选择因练习而异；每个相关步骤都会说明要求。

先使用当前说明文件发送提示。稍后更新说明后，再次发送相同提示，比较结果。

1. 关闭之前练习留下的编辑器标签页，让 Copilot 只获取所需上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 了解辅助函数的位置。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，以便 Copilot 应用文件更改。

   ![Chat 视图中智能体选择器的屏幕截图。](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更改，通常会同时修改辅助函数和测试。
6. 注意生成的辅助函数是一个类型化函数，以 `db` 客户端作为第一个参数，并返回类型化的发行商数组。这来自适用于 `src/lib/*.ts` 的 `.github/instructions/drizzle.instructions.md` 数据层规范。
7. 注意生成的代码**缺少** TSDoc 注释和文件级注释头。

> [!CAUTION]
> Copilot 具有概率性，有时即使未明确要求也可能添加文档注释。这没有问题；本练习的重点是更新说明后行为的一致性有所改善。

## 添加存储库级规范

`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加一条存储库编码规范：

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分（大约在第 27 行）。注意其中尚未要求代码文档，这就是生成的辅助函数没有注释的原因。
3. 在现有规范之后添加以下内容，要求 Copilot 为新代码添加文件注释头和 TSDoc 注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 存储库级的 `.github/copilot-instructions.md` 适合放置整个项目都适用的规范；`*.instructions.md` 则适合特定语言、文件类型或任务。

## 再次发送提示并观察变化

1. 返回 Copilot Chat 并选择 **New Chat** 清除上下文。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 聚焦正确的文件。
3. 发送与之前相同的提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 注意新文件现在会包含类似以下内容的文件注释头：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发行商数据访问辅助函数。
    * 提供从数据库检索发行商信息的函数。
    */
   ```

5. 注意函数现在会包含类似以下内容的 TSDoc 注释：

   ```typescript
   /**
    * 返回包含所有发行商 ID 和名称的列表。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 解析为发行商对象数组的 Promise。
    */
   ```

你刚刚引导 Copilot 遵循新的项目规范，并将其应用到下一练习会继续使用的真实代码中。

## 提交说明并推送分支

说明文件和其他资源一样，也应使用源代码管理：

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开终端。
2. 运行以下命令，确认说明更新和辅助函数更改均已存在：

   ```bash
   git status
   ```

3. 暂存并提交说明更新，以及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他配套更改（例如测试），也请添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送后，可以在 PR 中关联待办事项中的说明文件规范议题。Copilot 可以通过项目已配置的 GitHub 工具查找议题、创建关联的 PR，然后合并 PR。

> [!NOTE]
> 这是本练习中第一次让 Copilot 代表你操作 GitHub。项目模板已配置 GitHub 工具。Copilot 首次使用时，VS Code 可能会提示你登录 GitHub。按提示授权即可。后续练习会介绍这种连接如何工作（Model Context Protocol）。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确保智能体选择器中选中 **Agent**。
3. 让 Copilot 查找与更新说明文件相关的议题，并从当前分支创建 PR，说明该 PR 会关闭议题，同时包含为后续筛选工作添加的发行商辅助函数基础：

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 会查找议题并创建 PR。
5. Copilot 提示允许它代表你执行 GitHub 操作时，检查命令并酌情选择 **Allow**。
6. PR 创建后，让 Copilot 将 PR 合并到 `main`，随后在本地切回 `main` 并拉取最新代码：

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot 提示允许它执行 GitHub 操作和 shell 命令时，检查命令并酌情选择 **Allow**。

现在，你已借助 GitHub Copilot 创建并合并拉取请求。

## 总结和后续步骤

你了解了 Copilot 如何读取说明文件，并使用 VS Code 中的 Copilot Chat：

- 发送代码生成提示，观察现有说明的作用。
- 向 `.github/copilot-instructions.md` 添加新的全局规范。
- 再次发送相同提示，观察生成的代码如何采用新规范。
- 将说明更新和辅助函数基础提交并合并到 `main`，供下一练习使用。

接下来，在[智能体模式][next-lesson]中运用这些说明，让 Copilot 为筛选功能添加代码。

## 资源

- [GitHub Copilot 自定义说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [Copilot 自定义说明编写技巧][copilot-instructions-five-tips]
- [Copilot 个人自定义说明][personal-instructions]
- [Awesome Copilot：说明文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
