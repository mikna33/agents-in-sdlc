---
title: "课程 4 - 使用自定义说明指导 Copilot"
description: "查看存储库说明、添加文档规范，并将其应用到筛选功能代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，就应让 Copilot 能够获取相应指导。[说明文件][instruction-files]不仅描述你想要什么代码，也说明代码应如何组织。现在你已构建筛选功能，接下来将查看 Copilot 使用的说明、添加文档规范，并将其应用到代码中。

本课程将介绍如何：

- 了解存储库说明和按路径限定的说明文件如何传达给智能体。
- 更新说明文件，确保遵循编码规范。
- 查看说明文件对代码的影响。

## 场景

Tailspin Toys 有一套开发实践指南和要求，包括：

- 注释应解释意图和不明显的决策，而不是复述代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 说明用途、参数和返回值；如果有可注入的 `db` 参数，也要说明。
- 可复用的 Astro 组件应说明其 `Props` 契约；相关代码更改时，注释也应保持更新。
- 保留现有格式和 lint 指南。

通过说明文件，可以确保 Copilot 了解这些实践要求，并据此完成任务。

## 说明文件

自定义说明可以为 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。你可以指定编码规范、库以及希望添加的注释类型。说明既可以适用于整个存储库，也可以针对特定文件类型或任务。

说明文件有两种：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送给 Copilot 的单一说明文件。它应包含适用于大多数请求的项目级信息。
- `.github/instructions/*.instructions.md`：针对特定语言、文件类型或任务提供指南。

> [!NOTE]
> 其他说明格式及其支持情况因环境而异。依赖特定格式前，请查看[自定义说明支持参考][custom-instructions-support]。

## 查看项目中的自定义说明文件

入门项目已包含一组说明文件。先查看现有文件，再进行修改，以便观察更改的效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 查看项目简介和编码指南。这些说明适用于本存储库中与 Copilot 的所有交互。
4. 打开 `.github/instructions` 文件夹并查看其中的文件，包括 Astro、Drizzle 数据层、测试等说明。
5. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置了决定适用文件的 glob。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它对其他说明文件和项目现有文件的链接。这样可以将较大的说明集拆分成可复用的小文件，并提供可参考的示例。

## 更新说明文件以符合团队规范

现有说明文件已经打下良好基础，但还缺少一项要求。修改核心 `copilot-instructions.md` 文件，要求新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分（大约在第 35 行）。
2. 在最后一条后添加以下内容：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存。

## 使用更新后的说明

Copilot CLI 会在对话开始时加载存储库说明。保存说明后，退出并恢复筛选功能对话，让 Copilot 在保留功能上下文的同时加载新说明。

1. 如果筛选功能对话仍处于打开状态，输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上，恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选功能对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的说明：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff`，检查更改过的 TypeScript 文件。确认新添加的 TSDoc 注释准确说明了代码。

## 总结和后续步骤

你了解了 Copilot CLI 如何读取说明文件，并将新规范应用到筛选功能代码中。具体来说，你：

- 查看了存储库说明和按路径限定的说明文件。
- 更新了说明文件，添加文档规范。
- 查看了新规范对代码的影响。

接下来，将[自定义并运行可复用的 quality-checks 技能][next-lesson]，以一致的方式运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义说明][instruction-files]
- [自定义说明支持][custom-instructions-support]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [Awesome Copilot：说明文件及其他资源集合][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
