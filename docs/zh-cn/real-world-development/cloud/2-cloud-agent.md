---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

几乎所有组织都会面临技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体能够自主执行更新代码和添加功能等任务。完成工作后，智能体会创建草稿拉取请求，供开发人员审查。这样可以将繁琐任务交给智能体，加快开发进程，让开发人员专注于更宏观的工作。

你将探索 Copilot 云智能体的以下功能：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 了解明确界定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初受雇开发网站的承包商没有妥善编写文档——事实上，你会发现项目完全缺少文档。首先，他们希望为应用程序中的所有导出函数添加 TSDoc 文档注释。

此外，设计团队准备改进游戏发现体验。他们希望每个游戏详情页都显示相关游戏，也就是同一类别中的其他游戏，让用户可以继续浏览。他们暂时不需要完善的实现，只需要一个可用于用户体验验收测试的版本。目前这项工作受阻，但还有优先级更高的其他议题。

这两项工作都可能很快被搁置，非常适合分配给 Copilot 云智能体。Copilot 云智能体可以异步处理任务，让开发人员专注于其他工作，然后再回来审查 Copilot 的成果并确认一切符合预期。

## 认识 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像人类开发人员一样在后台执行任务。和与人类开发人员协作一样，有多种方式可以委派工作，包括[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求以跟踪进度、设置环境并开始处理任务。你可以在 Copilot 会话进行期间或完成后查看详情。解决方案准备好供你审查时，Copilot 会在拉取请求中提及你。

## 明确界定指令范围的重要性

GitHub Copilot 并非魔法——它无法替你省去思考。即使看似简单的操作，深入分析后也会发现其复杂性，因此[要谨慎界定分配给 Copilot 云智能体的任务范围][cloud-agent-best-practices]。把它当作 AI 结对编程助手：分阶段工作、不断学习、进行试验并随时调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 此存储库中的自定义指令

前面的练习介绍了自定义指令及其如何指导 Copilot。如果你已完成自定义指令实操，就已经见过 `.github/copilot-instructions.md` 的作用；如果还没有，现在可以快速查看一下。将工作分配给 Copilot 云智能体前，先只读查看此存储库中已有的指令文件，稍后便能观察它们的影响。

在存储库的 GitHub Web 界面中打开以下文件；如果已启动 codespace，也可以在那里打开：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其留意 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将这些指令限定为适用于 `**/*.test.ts` 文件。

在下一节将 `Code lacks documentation` 议题分配给云智能体时，留意生成的拉取请求是否采用 TSDoc 文档注释、TypeScript 约定和注释头；这些要求来自上述指令文件。之后的练习会在审查拉取请求时再次介绍这一点。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要特定环境并运行一些设置脚本，以确保一切就绪。将任务分配给 Copilot 时也是如此，因为它会以类似软件工程师（SWE）的方式执行任务。

云智能体使用 [GitHub Actions][github-actions] 执行工作。可以通过创建[专用设置工作流][setup-workflow]来自定义环境，该工作流配置在 `.github/workflows/copilot-setup-steps.yml` 文件中，并在云智能体开始工作前运行。这样，它便可以使用所需的开发工具和依赖项。为确保实验流程顺畅并提供实践机会，我们已预先配置此工作流。它会为 Copilot 安装 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及单体 Astro 应用程序已迁移并填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"
# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch
env:
  ASTRO_TELEMETRY_DISABLED: "1"
jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5
      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"
      - name: Install JavaScript dependencies
        run: npm ci
      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium
      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

该文件与其他 GitHub 工作流文件类似，但有几个关键点：

- 它只包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会运行此作业。
- 留意 `workflow_dispatch` 触发器，它允许你从存储库的 **Actions** 选项卡手动运行工作流。这样无需等待 Copilot 运行工作流，即可测试它是否能成功运行。

## 添加文档

虽然人人都知道文档的重要性，但大多数项目的信息要么已过时，要么完全缺失。这类技术债务常常得不到处理，进而降低效率，并增加维护代码库或让新开发人员加入团队的难度。幸运的是，Copilot 擅长生成文档，因此这项工作很适合分配给 Copilot 云智能体。它会在后台生成所需文档。之后的练习中，你会回来审查它的工作。

1. 在新的浏览器标签页中，导航到 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建新议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

   ![Copilot 分配详情](../../../_images/ex4-assign-copilot-details.png)

10. 选择 **Pull Requests** 选项卡。
11. 打开新创建的拉取请求（PR），其标题类似于 `[WIP]: Code lacks documentation`。如果列表中没有出现新的 PR，请稍等片刻，然后刷新浏览器窗口。
12. 几分钟后，你应该会看到 Copilot 创建的待办事项列表。

> [!NOTE]
> Copilot 的待办事项列表可能需要几分钟才会显示在 PR 中。Copilot 正在创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方法。

13. 查看列表和 Copilot 即将完成的任务。
14. 向下滚动拉取请求时间线，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 浏览实时会话，了解 Copilot 如何解决问题，包括探索代码并了解当前状态、暂停思考并确定合适的计划，以及编写代码。

这可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们能专注于其他工作。接下来，我们会利用这一点：先将另一项任务分配给 Copilot 云智能体，然后着手编写代码，为应用程序添加功能。

## 在游戏详情页中添加相关游戏部分

如前所述，GitHub Copilot 云智能体的一大优势是可以拆分工作：你专注于一组任务，同时让它处理另一组任务。为设计团队添加相关游戏部分也许不会花费太多时间，但这段时间仍可用于其他工作。现在就将此任务分配给 Copilot 云智能体！

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:
  - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
  - Show a "Related games" section on the game details page that uses the helper
  - Handle the case where a game has no related games
  - There should be unit tests created for the new helper
  - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，你应该会在议题的第一条评论中看到一组 👀，表示 Copilot 已开始处理！

![Copilot 使用眼睛表情符号表示它正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在认真处理你的第二个请求！Copilot 云智能体的工作方式类似软件工程师（SWE），因此你无需主动监控，而可以等任务完成后再进行审查。接下来，我们将了解如何创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。你可以将议题分配给云智能体，让它异步处理。可以让 Copilot 解决技术债务、创建新功能，或协助将代码从一个框架迁移到另一个框架。

你探索了以下概念：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 了解明确界定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台处理任务，此时我们可以开始创建和使用自定义智能体。[Copilot 云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并支持我们在前面模块中介绍过的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
