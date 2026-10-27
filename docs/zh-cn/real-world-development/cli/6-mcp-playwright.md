---
title: "课程 6 - 使用 Playwright 模型上下文协议（MCP）验证功能"
description: "在 Copilot CLI 中检查或配置 Playwright MCP，并通过浏览器探索筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

如前所述，编写代码不只是写代码。我们需要处理数据、外部服务，还需要为 Copilot 提供更多自动化能力。这时就需要 MCP 服务器。MCP 服务器可以扩展 Copilot CLI 的内置功能，为它提供更多工具和服务。

在本课中，你将：

- 了解模型上下文协议（MCP）是什么，以及 Copilot CLI 如何使用它。
- 如果尚未配置，则添加 Playwright MCP 服务器。
- 要求智能体操作浏览器并探索筛选功能。

## 场景

单元测试和端到端测试固然重要，但要验证用户界面（UI）更新，就必须实际与 UI 交互。你希望 Copilot 能像用户一样使用正在开发的网站，进一步自动化更改流程，并更有把握地确认更新符合预期。

## 什么是模型上下文协议（MCP）？

[模型上下文协议（MCP）][mcp-blog-post]为人工智能（AI）智能体提供了与外部工具和服务通信的方式。通过 MCP，AI 智能体可以实时访问外部工具和服务，从而获取最新信息并代表你执行操作。

智能体通过 MCP 服务器访问这些工具和资源。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁，每个 MCP 服务器都代表智能体可访问的一组不同工具和资源。

以下是几个常见的 MCP 服务器：

- **[GitHub MCP 服务器][github-mcp]**：提供用于管理 GitHub 存储库、议题和拉取请求的应用程序编程接口（API）。
- **[Playwright MCP 服务器][playwright-mcp-server]**：使用 Playwright 提供浏览器自动化功能。

还有许多其他可用的 MCP 服务器。GitHub 提供了一个 [MCP 注册表][mcp-registry]，帮助大家发现并为生态系统做出贡献。

> [!CAUTION]
> 将 MCP 服务器视作项目中的其他依赖项。使用前，请审查其源代码、验证发布者并考虑安全影响。

## 添加 Playwright MCP 服务器

如果尚未配置 Playwright MCP 服务器，请添加它，让 Copilot 可以像用户一样与网站交互。

> [!NOTE]
> 如果 Copilot CLI 中已配置 Playwright MCP，请跳过这些设置步骤，继续下一节。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器对话框：

   ```plaintext
   /mcp add
   ```

3. 输入名称 `playwright`，然后按 <kbd>Tab</kbd>。
4. 按 <kbd>Enter</kbd> 确认服务器类型为 **STDIO**，然后按 <kbd>Tab</kbd>。
5. 在 **Command** 对话框中粘贴以下命令：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 要求 Copilot 通过 Playwright 探索功能

之前你已手动确认功能符合预期。现在使用 Copilot 中配置的 Playwright MCP 服务器完成同样的检查！

1. 使用以下提示，要求 Copilot 通过 Playwright MCP 服务器验证功能：

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> 实际上，无需明确要求 Copilot 使用 MCP 服务器，因为它通常会自行判断。但既然你知道应该使用哪个服务器，明确指出也有好处！这样可以提高结果的一致性，并节省一些 token。

2. 观察 Copilot 如何列出它在浏览器中执行的各个步骤，以确认功能是否正常。
3. 阅读报告，确认一切行为符合预期。

Copilot 会启动服务器，使用 Playwright 与网站交互，停止服务器，然后向你提供报告。

## 总结和后续步骤

恭喜，你已通过 Copilot CLI 使用 Playwright MCP 服务器在真实浏览器中探索功能！回顾一下，你：

- 了解了模型上下文协议（MCP）是什么，以及 Copilot CLI 如何使用它。
- 如果尚未配置，则添加了 Playwright MCP 服务器。
- 要求智能体操作浏览器并探索筛选功能。

接下来，[创建 QA 自定义智能体][next-lesson]，将技能和浏览器工具整合到专门角色中。

## 资源

- [MCP 到底是什么，为什么大家都在讨论它？][mcp-blog-post]
- [Microsoft Playwright MCP 服务器][playwright-mcp-server]
- [向 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
