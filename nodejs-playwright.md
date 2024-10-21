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
