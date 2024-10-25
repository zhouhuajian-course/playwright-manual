# Node.js Playwright

1. 典型的例子 `https://playwright.dev/docs/api/class-playwright`
```javascript
const { chromium, firefox, webkit } = require('playwright');

(async () => {
  const browser = await chromium.launch();  // Or 'firefox' or 'webkit'.
  const page = await browser.newPage();
  await page.goto('http://example.com');
  // other actions...
  await browser.close();
})();
```
2. Playwright Test 专为满足端到端测试的需求而创建。
3. 首先使用 npm、yarn 或 pnpm 安装 Playwright。或者，您也可以使用VS Code 扩展开始并运行测试。
4. 您可以在playwright.config中添加 Playwright 的配置，包括修改要在哪些浏览器上运行 Playwright。
5. By default tests will be run on all 3 browsers, chromium, firefox and webkit using 3 workers. 
6. 测试以无头模式运行，这意味着运行测试时不会打开任何浏览器。测试结果和测试日志将显示在终端中。
7. npx playwright test
8. 测试完成后，将生成HTML 报告器，其中显示测试的完整报告，您可以按浏览器、通过的测试、失败的测试、跳过的测试和不稳定的测试来筛选报告。您可以点击每个测试并探索测试的错误以及测试的每个步骤。默认情况下，如果某些测试失败，则会自动打开 HTML 报告。
9. npx playwright show-report
10. npx playwright test --ui 使用UI 模式运行测试，以获得更好的开发人员体验，包括时间旅行调试、监视模式等。Run your tests with UI Mode for a better developer experience with time travel debugging, watch mode and more.
11. 系统要求
```
Node.js 18+
Windows 10+、Windows Server 2016+ 或 Windows Subsystem for Linux (WSL)。
macOS 13 Ventura 或 macOS 14 Sonoma。
Debian 11、Debian 12、Ubuntu 20.04 或 Ubuntu 22.04、Ubuntu 24.04，基于 x86-64 和 arm64 架构。
```
12. 执行操作之前无需等待任何事情：Playwright 会在执行每个操作之前自动等待各种可操作性检查通过。
13. `// @ts-check`在 VS Code 中使用 JavaScript 时在每个测试文件的开头添加以获得自动类型检查。
14. 定位器代表一种随时在页面上查找元素的方法
15. 断言
```
Playwright以函数形式包含测试断言expect。要做出断言，请调用expect(value)并选择一个反映期望的匹配器。

有许多通用匹配器（如toEqual、toContain）toBeTruthy可用于断言任何条件。

expect(success).toBeTruthy();

Playwright 还包括异步匹配器，它将等待直到满足预期条件。使用这些匹配器可以使测试更稳定且更具弹性。例如，此代码将等待直到页面获取包含“Playwright”的标题：

await expect(page).toHaveTitle(/Playwright/);
```
16. 测试隔离。Playwright Test 基于测试装置的概念，例如内置页面装置，它会传递到您的测试中。由于浏览器上下文，页面在测试之间是隔离的，这相当于一个全新的浏览器配置文件，每个测试都会获得一个全新的环境，即使在单个浏览器中运行多个测试也是如此。
17. 使用测试钩子。您可以使用各种测试钩子，例如test.describe声明一组测试和test.beforeEach在test.afterEach每个测试之前/之后执行的测试。其他钩子包括在所有测试之前/之后每个工作器执行一次的测试test.beforeAll和。test.afterAll
18. Playwright 具有生成测试的功能，是快速开始测试的好方法。它将打开两个窗口，一个浏览器窗口，您可以在其中与要测试的网站进行交互，另一个是 Playwright Inspector 窗口，您可以在其中记录测试、复制测试、清除测试以及更改测试的语言。
19. 运行 Codegen。
```
使用codegen命令运行测试生成器，后跟要为其生成测试的网站的 URL。URL 是可选的，您可以随时运行该命令而不使用它，然后将 URL 直接添加到浏览器窗口中。

npx playwright codegen demo.playwright.dev/todomvc
```
20. 如果生成器识别出与定位器匹配的多个元素，它将改进定位器以使其具有弹性并唯一地标识目标元素，therefore eliminating and reducing test(s) failing and flaking due to locators.
21. 单击工具栏中的某个图标，然后单击页面上要断言的元素，即可进行断言。您可以选择：
```
'assert visibility'断言某个元素可见
'assert text'断言元素包含特定文本
'assert value'断言某个元素有特定的值
```
22. 完成后关闭 Playwright 检查器窗口或停止终端命令。
23. 运行和调试测试
```
使用 Playwright，您可以运行单个测试、一组测试或所有测试。可以使用--project标志在一个或多个浏览器上运行测试。默认情况下，测试并行运行，并以无头方式运行，这意味着在运行测试时不会打开任何浏览器窗口，结果将显示在终端中。但是，您可以使用 CLI 参数在有头模式下运行测试，也可以使用标志在UI 模式--headed下运行测试。查看测试的完整跟踪，包括监视模式、时间旅行调试等。--ui
```
24. 您可以使用命令运行测试playwright test。这将在文件中配置的所有浏览器上运行测试playwright.config。测试默认以无头模式运行，这意味着运行测试时不会打开任何浏览器窗口，结果将显示在终端中。
25. !!! 我们强烈建议您使用UI 模式运行测试，以获得更好的开发人员体验，您可以轻松完成测试的每个步骤，并直观地查看每个步骤之前、期间和之后发生的情况。UI 模式还附带许多其他功能，例如定位器选择器、监视模式等。
26. 要在有头模式下运行测试，请使用该--headed标志。这将使您能够直观地看到 Playwright 如何与网站互动。npx playwright test --headed
27. 在不同的浏览器上运行
```
要指定您想要在哪个浏览器上运行测试，请使用--project标志后跟浏览器的名称。

npx playwright test --project webkit

要指定多个浏览器来运行测试，请--project多次使用该标志，后跟每个浏览器的名称。

npx playwright test --project webkit --project firefox
```
28. Run specific tests 运行特定的测试用例
```
To run a single test file, pass in the name of the test file that you want to run.

npx playwright test landing-page.spec.ts

To run a set of test files from different directories, pass in the names of the directories that you want to run the tests in.

npx playwright test tests/todo-page/ tests/landing-page/

To run files that have landing or login in the file name, simply pass in these keywords to the CLI.

npx playwright test landing login

To run a test with a specific title, use the -g flag followed by the title of the test.

npx playwright test -g "add a todo item"
```
29. 运行最后失败的测试。要仅运行上次测试运行中失败的测试，请先运行测试，然后使用--last-failed标志再次运行它们。npx playwright test --last-failed
30. 在 VS Code `https://playwright.dev/docs/getting-started-vscode`
```
可以使用VS Code 扩展直接从 VS Code 运行测试。安装后，您只需单击要运行的测试旁边的绿色三角形，或从测试侧栏运行所有测试即可。查看我们的VS Code 入门指南了解更多详细信息。
```
31. !!! 我们强烈建议您使用UI 模式调试测试，以获得更好的开发人员体验，您可以轻松完成测试的每个步骤，并直观地查看每个步骤之前、期间和之后发生的情况。UI 模式还附带许多其他功能，例如定位器选择器、监视模式等。
32. !!! 使用 Playwright 调试
```
要调试所有测试，请运行 Playwright 测试命令，然后运行--debug标志。

npx playwright test --debug
```
33. 调试方式 使用VS Code 调试器、UI 模式和Playwright Inspector进行调试以及使用浏览器开发人员工具进行调试
34. npx playwright show-report name-of-my-extracted-playwright-report
35. VS Code 扩展  `Playwright Test for VSCode`
```
安装后，打开命令面板并输入：

Install Playwright

```

todo npm init playwright@latest
npx playwright test --ui
或从测试侧栏运行所有测试即可

spec
