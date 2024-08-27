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
