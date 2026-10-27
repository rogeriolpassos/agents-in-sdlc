---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

在智能体模式下使用 GitHub Copilot 时，它会自主探索代码库、提出更改建议、编辑文件并运行命令。由于这些工作会在你的计算机上实时执行，VS Code 中的 Copilot Chat 会持续显示每次工具调用和文件编辑。你可以直接在 Chat 中审查每项差异、接受或拒绝单项更改，并通过后续提示引导对话，在不离开编辑器的情况下完善或扩展工作。

在本练习中，你将：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行期间引导智能体，在高对比度模式之外添加浅色模式切换。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队又提出了一项需求：他们也希望为觉得默认主题太暗的用户添加浅色模式切换。与其从头开始新对话，不如引导现有会话扩展工作范围。

## 监控 Copilot 的进度

Copilot Chat 视图会显示智能体执行的每个步骤，包括探索的文件、运行的命令和提出的编辑。每项文件编辑都会以内联方式显示在 Chat 中，并附带差异以及 **Keep** / **Undo** 控件。

1. 返回练习 4 中使用的 Copilot Chat 视图。
2. 智能体工作时，滚动 Chat 查看每项工具调用。你会看到文件读取、文件编辑，以及它请求运行的终端命令。
3. 智能体提出文件编辑时，差异会以内联方式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 还原更改，让智能体重新尝试。
4. 也可以打开 Chat 顶部的 **Files changed** 指示器，查看智能体在本次会话中修改的完整工作文件集。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。只批准你认可的命令。

5. 等待智能体完成高对比度模式的实现。你应该会看到一条总结所做更改的最终消息。

## 引导智能体添加浅色模式

高对比度模式已就绪，现在扩展同一对话，添加浅色模式切换。由于继续使用同一 Chat 会话，智能体会保留之前工作的所有上下文，包括修改过的组件、使用的样式方案和设置的本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

   ```plaintext
   Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
   ```

2. 智能体会继续处理，探索之前所做的更改，并提出添加浅色模式的方案。
3. 每项新编辑完成后都进行审查，就像审查高对比度功能一样。对每项差异使用 **Keep** 或 **Undo**。
4. 如果智能体的处理方向不符合你的预期——例如它试图以你不喜欢的方式重构切换用户界面（UI）——可以选择 Chat 输入框旁边的 **Stop** 按钮停止任务，然后发送澄清后的后续消息。

> [!TIP]
> 引导进行中的会话比从头开始更快。可以用后续提示要求调整（“简化 CSS——使用 CSS 变量，而不是重复选择器”）、纠正方向（“不要添加第三个切换项，把浅色模式整合到现有设置面板中”），或扩展范围（“还要为两个切换项添加键盘快捷键”）。

## 审查工作文件集

提交工作前，快速检查智能体修改过的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 查看所有更改文件的完整列表。你应该会看到 Astro 组件、样式和相关测试的更新。
3. 打开几个更改文件并查看差异。确认代码体现了自定义智能体所要求的无障碍模式——ARIA（Accessible Rich Internet Applications，可访问的富互联网应用程序）属性、键盘导航、语义化超文本标记语言（HTML），以及通过本地存储持久化设置。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。你将在[下一课][next-lesson]中发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 智能体模式监控并引导了正在进行的会话——逐项审查文件编辑、接受或拒绝更改，并通过后续提示扩展工作，同时保留现有上下文。

本课中，你探索了：

- 实时监控智能体的工作。
- 接受或拒绝单项文件编辑。
- 通过后续提示引导正在进行的会话。

在[下一课][next-lesson]中，你将把无障碍功能作为拉取请求发布，并审查本实验中本地自定义智能体生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
