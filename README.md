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
11. 
