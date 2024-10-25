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
23. 
todo npm init playwright@latest
npx playwright test --ui

