# playwright manual

https://github.com/microsoft/playwright-java

https://javadoc.io/doc/com.microsoft.playwright/playwright

https://www.youtube.com/@Playwrightdev

https://demo.playwright.dev/todomvc/#/

https://learn.microsoft.com/en-us/training/modules/build-with-playwright/


1. 截图
```java
import com.microsoft.playwright.*;

import java.nio.file.Paths;
import java.util.Arrays;
import java.util.List;

public class PageScreenshot {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      List<BrowserType> browserTypes = Arrays.asList(
        playwright.chromium(),
        playwright.webkit(),
        playwright.firefox()
      );
      for (BrowserType browserType : browserTypes) {
        try (Browser browser = browserType.launch()) {
          BrowserContext context = browser.newContext();
          Page page = context.newPage();
          page.navigate("https://playwright.dev/");
          page.screenshot(new Page.ScreenshotOptions().setPath(Paths.get("screenshot-" + browserType.name() + ".png")));
        }
      }
    }
  }
}
```
2. 电脑端运行 模拟 移动端 启动 Chromium 并 设置 手机的地理位置，国内访问不了 `https://www.openstreetmap.org/`
```java
import com.microsoft.playwright.options.*;
import com.microsoft.playwright.*;

import java.nio.file.Paths;

import static java.util.Arrays.asList;

public class MobileAndGeolocation {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().launch();
      BrowserContext context = browser.newContext(new Browser.NewContextOptions()
        .setUserAgent("Mozilla/5.0 (Linux; Android 8.0; Pixel 2 Build/OPD3.170816.012) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3765.0 Mobile Safari/537.36")
        .setViewportSize(411, 731)
        .setDeviceScaleFactor(2.625)
        .setIsMobile(true)
        .setHasTouch(true)
        .setLocale("en-US")
        .setGeolocation(41.889938, 12.492507)
        .setPermissions(asList("geolocation")));
      Page page = context.newPage();
      page.navigate("https://www.openstreetmap.org/");
      page.click("a[data-bs-original-title=\"Show My Location\"]");
      page.screenshot(new Page.ScreenshotOptions().setPath(Paths.get("colosseum-pixel2.png")));
    }
  }
}
```
3. 执行 JavaScript 脚本
```java
import com.microsoft.playwright.*;

public class EvaluateInBrowserContext {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.firefox().launch();
      BrowserContext context = browser.newContext();
      Page page = context.newPage();
      page.navigate("https://www.example.com/");
      Object dimensions = page.evaluate("() => {\n" +
        "  return {\n" +
        "      width: document.documentElement.clientWidth,\n" +
        "      height: document.documentElement.clientHeight,\n" +
        "      deviceScaleFactor: window.devicePixelRatio\n" +
        "  }\n" +
        "}");
      System.out.println(dimensions);
    }
  }
}
// {width=1280, height=720, deviceScaleFactor=1}
```
4. 拦截所有网络请求，打印所有网络请求URL，index、css、js、img、接口等
```java
import com.microsoft.playwright.*;

public class InterceptNetworkRequests {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.webkit().launch();
      BrowserContext context = browser.newContext();
      Page page = context.newPage();
      // 拦截所有网络请求
      page.route("**", route -> {
        System.out.println(route.request().url());
        // 继续
        route.resume();
      });
      // page.navigate("http://todomvc.com");
      page.navigate("http://192.168.30.1:3000/");
    }
  }
}
// http://192.168.30.1:3000/
// http://192.168.30.1:3000/assets/index-DuZ2DQuV.js
// http://192.168.30.1:3000/assets/index-7V1J10Kq.css
```
5. 打印页面title
```java
package org.example;

import com.microsoft.playwright.*;

public class App {
    public static void main(String[] args) {
        try (Playwright playwright = Playwright.create()) {
            Browser browser = playwright.chromium().launch();
            Page page = browser.newPage();
            page.navigate("http://playwright.dev");
            System.out.println(page.title());
        }
    }
}
```
6. 浏览器上下文， Browser context is equivalent to a brand new browser profile.浏览器上下文，等同于一个全新的浏览器配置文件。可以 保存上下文的身份验证状态并在所有测试中重复使用。
7. `mvn compile exec:java -D exec.mainClass="org.example.App"` 运行它会下载 Playwright 包并安装 Chromium、Firefox 和 WebKit 的浏览器二进制文件。
8. 安装浏览器 `https://playwright.dev/java/docs/browsers#install-browsers`
```bash
# 安装默认浏览器
$ mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="install"
# 安装特定浏览器
$ mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="install webkit"
# 查看所有支持的浏览器
$ mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="install --help"
```
9. 根据环境变量 `BROWSER` 使用不同浏览器
```java
package com.company.demo;

import com.microsoft.playwright.*;

public class Demo01 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = null;
      String browserName = System.getenv("BROWSER");
      BrowserType.LaunchOptions options = new BrowserType.LaunchOptions().setHeadless(false);
      if (browserName.equals("chromium")) {
        browser = playwright.chromium().launch(options);
      } else if (browserName.equals("firefox")) {
        browser = playwright.firefox().launch(options);
      } else if (browserName.equals("webkit")) {
        browser = playwright.webkit().launch(options);
      }
      Page page = browser.newPage();
      page.navigate("http://localhost:8080/todo/index.html");
      System.out.println(page.title());
      page.waitForTimeout(5000);
    }
  }
}
```
10. 不同版本的 playwright 使用不同版本的 playwright 构建的 浏览器 例如 `chromium-1129` `chromium-1097` 其中版本号是 playwright 构建浏览器的版本号，不是浏览器本身的版本号
11. 浏览器使用 chromium 可以尽早发现问题，等 chrome 或 edge 发布前修复，但 `话虽如此，测试政策通常要求针对当前公开可用的浏览器进行回归测试。在这种情况下，您可以选择其中一个稳定渠道，"chrome"或者"msedge"。` `Google Chrome 和 Microsoft Edge 尊重企业政策，其中包括对功能、网络代理、强制扩展的限制，这些都会阻碍测试。因此，如果您是使用此类政策的组织的一部分，最简单的方法是使用捆绑的 Chromium 进行本地测试，您仍然可以选择加入通常不受此类限制的机器人的稳定渠道。` 如何选择浏览器 参考 `https://playwright.dev/java/docs/browsers#when-to-use-google-chrome--microsoft-edge-and-when-not-to`
12. 浏览器安装位置 `PLAYWRIGHT_BROWSERS_PATH`
```text
Playwright 将 Chromium、WebKit 和 Firefox 浏览器下载到特定于操作系统的缓存文件夹中：

%USERPROFILE%\AppData\Local\ms-playwright在 Windows 上
~/Library/Caches/ms-playwright哪里 macOS
~/.cache/ms-playwright在 Linux 上
这些浏览器安装后将占用几百兆的磁盘空间：

du -hs ~/Library/Caches/ms-playwright/*
281M  chromium-XXXXXX
187M  firefox-XXXX
180M  webkit-XXXX

您可以使用环境变量覆盖默认行为。安装 Playwright 时，要求它将浏览器下载到特定位置

PowerShell
$Env:PLAYWRIGHT_BROWSERS_PATH="$Env:USERPROFILE\pw-browsers"
$Env:PLAYWRIGHT_BROWSERS_PATH="E:\playwright"
mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="install"

运行 Playwright 时，也要设置环境变量 PLAYWRIGHT_BROWSERS_PATH，这样才能搜索到 浏览器
```
13. 卸载浏览器 `https://playwright.dev/java/docs/browsers#uninstall-browsers` `mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="uninstall --all"`
14. 设置浏览器无头模式 `默认情况下，Playwright 以无头模式运行浏览器。` `playwright.firefox().launch(new BrowserType.LaunchOptions().setHeadless(false).setSlowMo(50));`
15. 减慢执行速度 单位毫秒 `setSlowMo()` `playwright.firefox().launch(new BrowserType.LaunchOptions().setHeadless(false).setSlowMo(50));` `https://javadoc.io/doc/com.microsoft.playwright/playwright/latest/com/microsoft/playwright/BrowserType.LaunchOptions.html#setSlowMo(double)`
16. 使用 `mvn` 的方式执行 ` mvn compile exec:java -D exec.mainClass="com.company.demo.Demo02"`
17. 系统要求，例如 Java 版本要求 `https://playwright.dev/java/docs/intro#system-requirements`
18. `检查会自动重试，直到满足必要条件。Playwright 内置了自动等待功能，这意味着它会在执行操作之前等待元素可操作。`
19. 使用 `assertThat` 进行断言
```java
package org.example;

import java.util.regex.Pattern;
import com.microsoft.playwright.*;
import com.microsoft.playwright.options.AriaRole;

import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

public class App {
    public static void main(String[] args) {
        try (Playwright playwright = Playwright.create()) {
            Browser browser = playwright.chromium().launch();
            Page page = browser.newPage();
            page.navigate("http://playwright.dev");

            // Expect a title "to contain" a substring.
            assertThat(page).hasTitle(Pattern.compile("Playwright"));

            // create a locator
            Locator getStarted = page.getByRole(AriaRole.LINK, new Page.GetByRoleOptions().setName("Get Started"));

            // Expect an attribute "to be strictly equal" to the value.
            assertThat(getStarted).hasAttribute("href", "/docs/intro");

            // Click the get started link.
            getStarted.click();

            // Expects page to have a heading with the name of Installation.
            assertThat(page.getByRole(AriaRole.HEADING,
               new Page.GetByRoleOptions().setName("Installation"))).isVisible();
        }
    }
}
```
20. assertThat 传不同的 对象 ，断言方法也不同，例如 `Page` `Locator` `APIResponse`
```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.microsoft.playwright.assertions;

import com.microsoft.playwright.APIResponse;
import com.microsoft.playwright.Locator;
import com.microsoft.playwright.Page;
import com.microsoft.playwright.impl.APIResponseAssertionsImpl;
import com.microsoft.playwright.impl.AssertionsTimeout;
import com.microsoft.playwright.impl.LocatorAssertionsImpl;
import com.microsoft.playwright.impl.PageAssertionsImpl;

public interface PlaywrightAssertions {
  static APIResponseAssertions assertThat(APIResponse response) {
    return new APIResponseAssertionsImpl(response);
  }

  static LocatorAssertions assertThat(Locator locator) {
    return new LocatorAssertionsImpl(locator);
  }

  static PageAssertions assertThat(Page page) {
    return new PageAssertionsImpl(page);
  }

  static void setDefaultAssertionTimeout(double timeout) {
    AssertionsTimeout.setDefaultTimeout(timeout);
  }
}
```

```java
package com.company.demo;

import java.util.regex.Pattern;
import com.microsoft.playwright.*;
import com.microsoft.playwright.options.AriaRole;

import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

public class Demo03 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().launch(new BrowserType.LaunchOptions().setHeadless(false));
      Page page = browser.newPage();
      page.navigate("http://localhost:8080/todo/index.html");

      assertThat(page).hasTitle(Pattern.compile("TodoMVC"));

      Locator input = page.getByPlaceholder("What needs to be done?");

      assertThat(input).hasAttribute("class", "new-todo");
      input.fill("Learn playwright!");
      input.press("Enter");
      page.waitForTimeout(5000);
    }
  }
}
```
21. `Playwright 有一个BrowserContext的概念，它是一个内存中隔离的浏览器配置文件。建议为每个测试创建一个新的BrowserContext，以确保它们不会互相干扰。`
22. 启动 代码生成器 `Codegen` `mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="codegen demo.playwright.dev/todomvc"`
23. `codegen` 优先考虑角色 role、文本 text 和测试 ID 定位器，`如果生成器识别出与定位器匹配的多个元素，它将改进定位器以使其具有弹性并唯一地标识目标元素，从而消除和减少由于定位器而导致的测试失败和flaking。`。完成后关闭 Playwright 检查器窗口或停止终端命令。
```text
'assert visibility'断言某个元素可见
'assert text'断言元素包含特定文本
'assert value'断言某个元素有特定的值
```
24. 使用 `JUnit` 并且每个测试都有自己的 `BrowserContext` 和 `Page`，不同的上下文，其实做了重新启动浏览器的操作，关闭窗口，重新启动了，使用了不同配置
```java
package com.company;

import com.microsoft.playwright.*;
import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

public class Demo04 {
  // Shared between all tests in this class.
  static Playwright playwright;
  static Browser browser;

  // New instance for each test method.
  BrowserContext context;
  Page page;

  @BeforeAll
  static void launchBrowser() {
    playwright = Playwright.create();
    browser = playwright.chromium().launch(new BrowserType.LaunchOptions().setHeadless(false).setSlowMo(5000));
  }

  @AfterAll
  static void closeBrowser() {
    playwright.close();
  }

  @BeforeEach
  void createContextAndPage() {
    context = browser.newContext();
    page = context.newPage();
  }

  @AfterEach
  void closeContext() {
    context.close();
  }

  @Test
  void shouldClickButton() {
    page.navigate("data:text/html,<script>var result;</script><button onclick='result=\"Clicked\"'>Go</button>");
    page.locator("button").click();
    assertEquals("Clicked", page.evaluate("result"));
  }

  @Test
  void shouldCheckTheBox() {
    page.setContent("<input id='checkbox' type='checkbox'></input>");
    page.locator("input").check();
    assertTrue((Boolean) page.evaluate("() => window['checkbox'].checked"));
  }

  @Test
  void shouldSearchWiki() {
    // page.navigate("https://www.wikipedia.org/");
    page.navigate("https://maven.org/");
    // page.locator("input[name=\"search\"]").click();
    // page.locator("input[name=\"search\"]").fill("playwright");
    // page.locator("input[name=\"search\"]").press("Enter");
    // assertEquals("https://en.wikipedia.org/wiki/Playwright", page.url());
  }
}
```
25. Playwright 操作跟踪，运行后 `trace.zip` 可以使用 `https://trace.playwright.dev/` 打开，或者 `Playwright CLI` 也可以打开 `mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="show-trace trace.zip"`，推荐用 CLI 打开，会启动窗口 `Playwright Trace Viewer`，非常方便
```java
package com.company.demo;

import com.microsoft.playwright.*;
import com.microsoft.playwright.options.LoadState;
import com.microsoft.playwright.options.WaitUntilState;

import java.nio.file.Paths;
import java.util.regex.Pattern;

import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

public class Demo04 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().launch(new BrowserType.LaunchOptions().setHeadless(false));
      BrowserContext context = browser.newContext();

      // Start tracing before creating / navigating a page.
      context.tracing().start(new Tracing.StartOptions()
              .setScreenshots(true)
              .setSnapshots(true)
              .setSources(true));

      Page page = context.newPage();
      page.route("**/*.{png,jpg,jpeg,js}", route -> route. abort());
      page.navigate("https://playwright.dev", new Page.NavigateOptions().setTimeout(0));
      page.navigate("https://playwright.dev/java/", new Page.NavigateOptions().setTimeout(0));
      page.navigate("https://playwright.dev/java/docs/", new Page.NavigateOptions().setTimeout(0));

      // Stop tracing and export it into a zip archive.
      context.tracing().stop(new Tracing.StopOptions()
              .setPath(Paths.get("trace.zip")));
    }
  }
}
```
26. 录制视频
```java
package learn;

import com.microsoft.playwright.*;
import com.microsoft.playwright.options.*;

import java.nio.file.Paths;

import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

public class Lesson01 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().launch(new BrowserType.LaunchOptions()
              .setHeadless(false));
      BrowserContext context = browser.newContext(new Browser.NewContextOptions().setRecordVideoDir(Paths.get("videos/")));

      Page page = context.newPage();
      page.navigate("http://localhost/article-add.html");
      page.waitForTimeout(3000);
      page.getByPlaceholder("标题").fill("这是标题");
      page.waitForTimeout(3000);
      page.getByPlaceholder("内容").fill("这是内容");
      page.waitForTimeout(3000);
      page.getByRole(AriaRole.BUTTON, new Page.GetByRoleOptions().setName("提交")).click();
      page.waitForTimeout(3000);
      assertThat(page.getByRole(AriaRole.HEADING)).containsText("添加成功");
      page.waitForTimeout(3000);

      // Make sure to close, so that videos are saved.
      context.close();
    }
  }
}
```
27. 设置录制视频的大小 `Browser.NewContextOptions options = new Browser.NewContextOptions().setRecordVideoDir(Paths.get("videos/")).setRecordVideoSize(1920, 1080);`
28. Mock API。page.route() 传字符串URL时，需要精准匹配，即使后面的?也要加上，当然这个例子可以优化，或者也可使用通配符 `https://globster.xyz/` 。官网文档 `RequestOptions.create()` 已过时，看源码的例子，都是使用 `new Route.FulfillOptions()`
```java
package learn;

import com.microsoft.playwright.*;
import com.microsoft.playwright.options.*;
import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

public class Lesson02 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().launch(new BrowserType.LaunchOptions()
              .setHeadless(false));
      BrowserContext context = browser.newContext();
      Page page = context.newPage();
      page.navigate("http://localhost/transfer.html");
      page.route("http://localhost/transfer?", route -> {
        // route.fulfill(RequestOptions.create().setData("添加成功！Mock API"));
        System.out.println("测试");
        route.fulfill(new Route.FulfillOptions().setBody("Success! Mock!"));
      });
      page.getByRole(AriaRole.TEXTBOX).click();
      page.getByRole(AriaRole.TEXTBOX).fill("小周");
      page.getByRole(AriaRole.SPINBUTTON).click();
      page.getByRole(AriaRole.SPINBUTTON).fill("100");
      page.waitForTimeout(5000);
      page.getByRole(AriaRole.BUTTON, new Page.GetByRoleOptions().setName("提交")).click();
      page.waitForTimeout(5000);
    }
  }
}
```
29. `context.newPage()` 会创建新的标签
30. 操作已打开的浏览器 `chrome.exe --remote-debugging-port=9222`
```java
package learn;

import com.microsoft.playwright.*;

public class Lesson04 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().connectOverCDP("http://localhost:9222/");
      System.out.println(browser.contexts());
      BrowserContext defaultContext = browser.contexts().get(0);
      System.out.println(defaultContext.pages());
      Page page = defaultContext.pages().get(0);
      System.out.println(page.title());
      Page page1 = defaultContext.pages().get(1);
      System.out.println(page1.title());
      Page page2 = defaultContext.newPage();
      page2.navigate("http://localhost/article-add.html");
      System.out.println(page2.title());

    }
  }
}
/*
[com.microsoft.playwright.impl.BrowserContextImpl@79079097]
[com.microsoft.playwright.impl.PageImpl@4d1c00d0, com.microsoft.playwright.impl.PageImpl@4b2bac3f]
transfer!!!
slogan!!!
article-add!!!
 */
```
31. 操作已打开浏览器 使用 WebSocket `chrome.exe --remote-debugging-port=9222 --enable-logging`
```text
C:\Program Files\Google\Chrome\Application>chrome.exe --remote-debugging-port=9222 --enable-logging

DevTools listening on ws://127.0.0.1:9222/devtools/browser/bb8ac345-2af0-4dd0-a355-cbe34068f24e
```

```java
package learn;

import com.microsoft.playwright.*;

public class Lesson04 {
  public static void main(String[] args) {
    try (Playwright playwright = Playwright.create()) {
      Browser browser = playwright.chromium().connectOverCDP("ws://127.0.0.1:9222/devtools/browser/bb8ac345-2af0-4dd0-a355-cbe34068f24e");
      System.out.println(browser.contexts());
      BrowserContext defaultContext = browser.contexts().get(0);
      System.out.println(defaultContext.pages());
      Page page = defaultContext.pages().get(0);
      System.out.println(page.title());
      Page page2 = defaultContext.newPage();
      page2.navigate("http://localhost/article-add.html");
      System.out.println(page2.title());
    }
  }
}
// [com.microsoft.playwright.impl.BrowserContextImpl@24b1d79b]
// [com.microsoft.playwright.impl.PageImpl@68ceda24]
// 新标签页
// article-add!!!
```
