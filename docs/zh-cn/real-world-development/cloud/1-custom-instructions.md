---
title: "练习 1 - 自定义说明（Cloud agent）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot cloud agent →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解背景信息，就应确保它能够获取这些上下文。[说明文件][instruction-files]可提供指导，让 Copilot 不仅了解你想让它做什么，也了解应该如何完成。

本练习将介绍如何：

- 了解项目上下文、编码指南和文档规范如何通过存储库自定义说明和按路径限定的说明文件传递给 Copilot。
- 向 `.github/copilot-instructions.md` 添加新的全局规范。

> [!NOTE]
> 与 VS Code 和 CLI 环境不同，本练习不会使用前后对比提示。Copilot cloud agent 会异步处理 GitHub 议题，因此难以实时并排展示效果。稍后审查 cloud agent 创建的 PR 时，可以看到说明文件的影响。

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

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送给 Copilot 的单一说明文件。它应包含适用于大多数聊天或 CLI 请求的项目级信息，例如技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型提供指南，例如 TypeScript、Astro、UI 组件或单元测试。

自定义说明没有唯一正确的写法。使用自然语言明确描述指导并提供正反示例，再根据项目实际情况不断试验和改进。

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

花些时间查看存储库自带的说明文件：一个核心 `copilot-instructions.md` 和一组适用于各种任务的 `*.instructions.md` 文件。可以在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。注意 **Code standards** 中的 **GitHub Actions Workflows** 子部分。这些内容适用于你与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览，其中包含 Astro、Drizzle 数据层、测试等说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它使用相对于存储库根目录的 glob 决定适用文件；例如，`**/*.test.ts` 会匹配 TypeScript 测试文件。
5. 查看该文件中针对单元测试的说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并查看底部链接。它引用了其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件。这样可以将较大的说明集拆分成可复用的小文件，并提供可参考的示例。（其中的路径相对于说明文件所在位置，而非存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码规范，但尚未要求在代码中编写文档。接下来会添加 TSDoc 注释和文件注释头规范。

## 添加存储库级规范

接下来只需编辑一次：在项目范围的规则中要求代码使用 TSDoc 文档注释和文件级注释头。稍后分配给 cloud agent 的议题会使用这项规范。

编辑前，先创建工作分支。cloud agent 会从议题目标分支读取说明，但本练习仍会单独提交更改：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` 用于提供项目级信息。重新打开该文件，找到 **Code formatting requirements** 部分，并在现有规范之后添加以下两行：

```markdown
- Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
- Before imports or any code, add a comment block to the file that explains its purpose.
```

保存 `copilot-instructions.md`。

> [!TIP]
> 存储库级的 `.github/copilot-instructions.md` 适合放置整个项目都适用的规范；`*.instructions.md` 则适合特定语言、文件类型或任务。

## 提交、推送并合并说明更新

cloud agent 会从议题目标分支（本练习中为 `main`）创建工作分支。因此，说明更改必须先合并到 `main`，cloud agent 才能在下一练习中使用。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并将其合并。下一练习将向 Copilot 分配议题，届时 cloud agent 会从 `main` 读取说明。

## 总结和后续步骤

你了解了 Copilot 如何读取本项目的说明文件，并向 `.github/copilot-instructions.md` 添加了新的全局规范。稍后的练习中，可以在 cloud agent 生成的拉取请求中看到该规范的作用。

接下来，将[第一个议题分配给 Copilot cloud agent][next-lesson]。

## 资源

- [GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义说明的五个建议][copilot-instructions-five-tips]
- [Awesome Copilot：说明文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
