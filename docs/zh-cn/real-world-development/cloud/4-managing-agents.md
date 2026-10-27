---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前两项练习中，你要求 Copilot 云智能体处理了三项不同的任务，目的是改善用户体验并添加功能。云智能体的设计目标是异步自主工作，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心，你可以查看所有具有未关闭拉取请求（PR）的智能体任务，了解已执行的操作，甚至可以在会话进行期间引导智能体。

在本课中，你将：

- 浏览智能体页面以监控云智能体任务。
- 引导正在进行的会话，请求添加其他功能。

## 场景

将实现高对比度模式的任务分配给智能体后，团队意识到现在也适合添加浅色模式。既然正在更新网站样式并添加切换功能，将浅色模式一并纳入便顺理成章。你将引导智能体的工作，确保它在实现高对比度模式的同时也添加浅色模式。

## 查看 Copilot 云智能体任务

先查看分配给 Copilot 云智能体的所有任务的当前状态。

1. 导航到[Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主窗格和左侧窗格中的任务列表。你应该能看到分配给 Copilot 的任务，包括：
   - 更新代码库文档。
   - 生成用于修改游戏的 API。
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的操作。这些操作可能包括：
   - 从存储库检出代码。
   - 为 Copilot 创建工作环境。
   - 设置 MCP 服务器。
   - 执行完成分配任务所需的各种步骤。

> [!NOTE]
> 具体显示的步骤会因 Copilot 当前的工作状态和采用的方法而异。

4. 另请留意右侧显示的拉取请求（PR）窗格。通过该窗格，可以查看 PR 和已更改的文件，以便进一步监控。

## 引导云智能体

了解当前运行的任务后，接下来要求 Copilot 在实现高对比度模式时添加浅色模式切换功能。

1. 选择与添加高对比度模式有关的会话。确切标题会因 Copilot 使用的名称和当前工作状态而异。

   ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到会话显示已完成设置并开始工作。看到类似下方的消息时，就表示已开始工作。
3. 在 **Steer active session while Copilot is working** 对话框中，添加以下提示：

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![智能体页面中的云智能体任务截图，其中突出显示了“Steer active session while Copilot is working”对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 留意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务！收到消息时，它会先完成当前步骤，然后将新请求加入工作流程。

和之前一样，这需要几分钟。此时可以暂停一下，回顾目前学到和探索的内容。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，它是监控和引导 GitHub Copilot 云智能体任务的中心位置。在这个任务控制中心，你可以跟踪所有正在进行和已完成的任务、审查正在执行的工作，还可以重定向正在进行的任务以调整范围或提供更多指导。

你探索了以下内容：

- 浏览任务控制中心和智能体页面，监控云智能体任务。
- 重定向正在进行的会话，请求添加其他功能。

Copilot 正在完成无障碍功能的实现。接下来，我们将[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
