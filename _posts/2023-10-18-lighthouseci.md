---
title: 性能守卫插件
categories: [学习笔记]
comments: true
---

## 性能守卫

性能守卫是一种系统或工具，用于监控和管理应用程序和系统的性能。它旨在确保应用程序在各种负载和使用情况下能够提供稳定和良好的性能。

## Lighthosuse

Lighthosuse 是一个开源的自动化工具，提供了四种使用方式

- Chrome DevTools

- Chrome 插件

- Node CLI

- Node 模块

借助 Lighthouse Node 模块继承到 CICD 流程中，这样在构建阶段就会知道页面具体性能，如果指标不合格，则不同意合并 MR

## 剖析 lighthouse-ci 实现

lighthouse-ci 实现步骤

1.【启动浏览器实例】：CLI 通过 Puppeteer 启动一个 Chorome 实例。

```
const browser = await puppeteer.launch();
```

2.【创建新的浏览器标签页】：接着，CLI 创建一个新的标签页（或称为页面）。

```
const page = await browser.newPage();
```

3.【导航到目标 URL】：CLI 命令浏览器加载指定的 URL

```
await page.goto('xxxx');
```

4.【收集数据】：在加载页面的同时，CLI 使用各种 Chrome 提供的 API 收集数据，包括网络请求数据、JavaScript 执行时间、页面渲染时间等。

5.【运行审计】：数据收集完成后，CLI 将这些数据传递给 Lighthouse 核心，该核心运行一系列预定义的审计。

6.【生成和返回报告】：最后，审计结果被用来生成一个 JSON 或 HTML 格式的报告。

```
const report = await lighthouse(url, opts, config).then(res => {
  return res?.report;
});
```

7.【关闭浏览器实例】：报告生成后，CLI 关闭 Chrome 实例。

```
await browser.close();
```

```
// 伪代码
const puppeteer = require('puppeteer');
const lighthouse = require('lighthouse');
const {URL} = require('url');

async function run() {
  // 使用puppeteer连接到Chrome浏览器
  const browser = await puppeteer.launch({
    headless: true,
    args: ['--no-sandbox', '--disable-setuid-sandbox'],
  });

  // 新建一个页面
  const page = await browser.newPage();

  const url = 'xxxx';

  // 使用Lighthouse进行审查
  const {lhr} = await lighthouse(url, {
    port: new URL(browser.wsEndpoint()).port,
    output: 'json',
    logLevel: 'info',
  });

  await browser.close();
}

run();

```

## 实现一个性能守卫插件

实现性能守卫插件，需考虑以下因数

- 【易用性和灵活性】：插件应易于配置和使用，以便它可以适应各种不同的 CI/CD 环境和应用场景。它也应能够适应各种不同的性能指标和阈值。

- 【稳定性和可靠性】：插件需可靠和稳定，因为它将影响整个构建流程。任何失败和错误都可能导致构建失败，所以需要有强大的错误处理和恢复能力。

- 【性能】：插件本身性能很重要，它直接影响构建的速度和效率。

- 【可维护性和扩展性】：插件应设计的易于维护和扩展，以便随着应用和需求的变化进行适当的修改和更新。

- 【报告和通知】：插件应提供清晰和有用的报告，以便开发人员快速理解和处理任何性能问题。也应该有一个通知系统，当性能指标低于预定阈值时，能够通知到相关人员。

- 【集成】：插件应能轻松集成到现有的 CI/CD 流程中，同时还应该支持各种流程的 CI/CD 工具和平台。

- 【安全性】：如果插件需要访问或处理敏感数据，就必须考虑安全性。应使用最佳的安全实践来保护数据。

```
const puppeteer = require('puppeteer');
const lighthouse = require('lighthouse');
const fs = require('fs');
const axios = require('axios');
const { promisify } = require('util');
const { port } = new URL(browser.wsEndpoint());

// promisify fs.writeFile for easier use
const writeFile = promisify(fs.writeFile);

async function runAudit(url, options = { port }) {
  // 使用Puppeteer启动Chrome
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  // 访问登录页面
  await page.goto('https://example.com/login');

  // 输入用户名和密码
  await page.type('#username', 'example_username');
  await page.type('#password', 'example_password');

  // 提交登录表单
  await Promise.all([
    page.waitForNavigation(), // 等待页面跳转
    page.click('#login-button'), // 点击登录按钮
  ]);

  // 运行Lighthouse
  const { lhr } = await lighthouse(url, options);

  // 保存审计结果到JSON文件
  const resultJson = JSON.stringify(lhr);
  await writeFile('lighthouse.json', resultJson);

  // 上传JSON文件到服务器
  const formData = new FormData();
  formData.append('file', fs.createReadStream('lighthouse.json'));

  // 上传文件到你的服务器
  const res = await axios.post('https://your-server.com/upload', formData, {
    headers: formData.getHeaders()
  });

  console.log('File uploaded successfully');

  await browser.close();
}

// 运行函数
runAudit('https://example.com');

```
