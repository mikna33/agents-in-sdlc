---
title: "课程 9 - GitHub Copilot CLI 中的斜杠命令"
description: "探索用于管理上下文、选择模型、共享会话，以及选择性并行处理和委派的斜杠命令。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

和其他优秀的 CLI 工具一样，GitHub Copilot CLI 也包含许多交互用的斜杠命令。这些命令提供高级功能、幕后信息和额外配置选项。你已使用过 `/diff`、`/mcp`、`/skills`、`/agent` 和 `/pr` 等命令，接下来看看其他实用命令。

本课程将介绍如何：

- 使用 `/context` 和 `/compact` 查看 Copilot 如何管理对话上下文。
- 使用 `/model` 查看可用模型。
- 了解 `/share` 如何导出或共享会话。
- 探索用于并行工作、工作树和委派给云智能体的可选命令。

## 场景

核心 CLI 工作流已完成。现在了解其他功能，包括管理上下文、切换模型、共享会话，以及选择性地将工作委派给 [Copilot cloud agent][about-cloud-agent]。

## 探索 Copilot CLI 上下文

处理大型或复杂任务时，可能会达到模型的最大上下文窗口。Copilot CLI 会在需要时自动压缩对话；你也可以使用斜杠命令自行检查或压缩上下文。

1. 返回 Codespace。如果 Copilot CLI 尚未打开，请从存储库根目录启动。
2. 输入：

   ```plaintext
   /context
   ```

3. 查看模型、当前 token 用量，以及上下文在系统说明、工具、消息和剩余空间之间的分配情况。
4. 压缩对话：

   ```plaintext
   /compact
   ```

5. 再次输入 `/context` 并比较结果。如果对话本来就很短，变化可能不明显。

> [!NOTE]
> Copilot CLI 会在上下文窗口接近填满时自动压缩内容。想自行决定时机，可使用 `/compact`。切换到无关任务并需要全新对话时，则使用 `/clear` 或 `/new`。

## 选择模型

不同模型各有优势，开发者也有各自偏好。Copilot CLI 可以列出并选择要使用的模型。

1. 输入：

   ```plaintext
   /model
   ```

2. 查看可用模型及其用量信息。
3. 保留当前模型、选择其他模型，或按 <kbd>Esc</kbd> 关闭列表。

## 共享会话

团队成员共享经验有助于大家提升 AI 工具的使用能力。`/share` 命令可以将会话导出为 Markdown 或 HTML 文件、创建可共享链接，或发布为 GitHub gist。

1. 输入 `/help`，查看当前安装版本中 `/share` 的选项。
2. 如需共享会话，请选择合适的目标，例如 `/share file`，导出为本地 Markdown 文件。
3. 发送给他人或发布之前，先审查导出内容。会话导出可能包含提示、回复和项目信息。

发布链接或 gist 是可选操作。不要发布团队不打算公开的存储库或对话内容。

## 可选：扩展并行工作或委派任务

核心工作坊已完成。Copilot CLI 还为大型任务提供了以下命令：

- `/fleet` 可将彼此独立的子任务分配给多个子智能体并行执行。
- `/worktree` 可创建隔离的 Git 工作树以处理单独任务。
- `/delegate` 可将任务发送给异步工作的 Copilot cloud agent；它可能会打开拉取请求。

这些命令是可选的，因为它们可能创建额外工作树或远程工作。试用前，请先创建范围明确的新任务，并通过常规工作流审查结果。若想深入了解异步智能体工作，可继续学习[云智能体工作坊][cloud-workshop]。

## 总结和后续步骤

Copilot CLI 斜杠命令可以帮助你配置工具、共享会话，并了解后台运行情况。本课程中，你：

- 使用 `/context` 和 `/compact` 查看 Copilot 如何管理上下文。
- 使用 `/model` 查看可用模型。
- 了解了 `/share` 如何导出会话。
- 探索了用于并行工作、工作树和委派给云智能体的可选命令。

Copilot CLI 还有更多斜杠命令值得探索。现在通过[回顾所学内容][next-lesson]并查看后续学习建议，结束本次学习。

## 资源

- [Copilot CLI 命令参考][cli-reference]
- [Copilot CLI 中的上下文管理][context-management]
- [关于 Copilot cloud agent][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
