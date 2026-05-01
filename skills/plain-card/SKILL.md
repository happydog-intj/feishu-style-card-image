---
name: plain-card
description: 简单卡片
triggers:
  - 简单卡片
  - plain card
  - 生成简单卡片
  - 做一张卡片
tools: [write_file, terminal, browser_navigate, browser_vision]
---

# 简单卡片生成

## 概述

生成简洁清爽的飞书风格卡片 PNG 图片。
技术栈：**纯 HTML + CSS → Puppeteer 截图（2x 高清）**。
左下角固定展示 `AI炼丹师` logo 签名。

Logo 已内嵌为 base64 data URI，无需任何外部文件，GitHub Actions 环境可直接使用。

---

## 步骤

### 1. 整理卡片内容

将信息归纳为以下区块（按需取舍）：
- **Header**：标题、副标题、图标 emoji
- **正文**：核心内容段落
- **要点列表**：条目化的关键信息
- **Footer**：固定签名（logo + `AI炼丹师`，不可修改）

### 2. 编写 HTML 文件

保存到 `/tmp/<name>_plain_card.html`，使用如下结构（footer 固定，必须包含）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: #f0f2f5;
    padding: 32px;
    font-family: -apple-system, "PingFang SC", "Helvetica Neue", sans-serif;
  }
  .card {
    background: #fff;
    border-radius: 12px;
    width: 600px;
    box-shadow: 0 2px 12px rgba(0,0,0,0.10);
    overflow: hidden;
  }

  /* ── Header ── */
  .card-header {
    background: linear-gradient(135deg, #1456F0 0%, #1890FF 100%);
    padding: 22px 24px 20px;
    display: flex; align-items: center; gap: 14px;
  }
  .header-icon { font-size: 36px; line-height: 1; }
  .header-text h1 {
    font-size: 20px; font-weight: 700; color: #fff;
    letter-spacing: -0.3px; margin-bottom: 4px;
  }
  .header-text p { font-size: 12.5px; color: rgba(255,255,255,0.82); }

  /* ── Body ── */
  .card-body { padding: 20px 24px 8px; }

  .section-title {
    font-size: 11px; font-weight: 700; color: #1456F0;
    letter-spacing: 0.8px; text-transform: uppercase;
    margin-bottom: 10px;
    display: flex; align-items: center; gap: 6px;
  }
  .section-title::after { content: ''; flex: 1; height: 1px; background: #e8edf5; }
  .section { margin-bottom: 18px; }

  .desc-block {
    background: #f7f8fc;
    border-left: 3px solid #1456F0;
    border-radius: 0 8px 8px 0;
    padding: 12px 14px;
    font-size: 13.5px; color: #333; line-height: 1.75;
  }

  /* ── Footer（固定，请勿修改） ── */
  .card-footer {
    background: #f7f8fc; border-top: 1px solid #eaecf3;
    padding: 11px 20px;
    display: flex; align-items: center; gap: 10px;
  }
  .footer-logo {
    width: 28px; height: 28px; border-radius: 50%;
    object-fit: cover; flex-shrink: 0;
  }
  .footer-name { font-size: 13px; font-weight: 600; color: #333; }
  .footer-right { margin-left: auto; font-size: 11.5px; color: #999; }
</style>
</head>
<body>
<div class="card">

  <!-- Header（根据内容替换） -->
  <div class="card-header">
    <div class="header-icon">✨</div>
    <div class="header-text">
      <h1>卡片标题</h1>
      <p>副标题或来源</p>
    </div>
  </div>

  <!-- Body（根据内容填充） -->
  <div class="card-body">
    <div class="section">
      <div class="section-title">核心内容</div>
      <div class="desc-block">
        在这里填写主要内容……
      </div>
    </div>
  </div>

  <!-- Footer（固定，必须保持此结构，src 为 base64 data URI） -->
  <div class="card-footer">
    <img class="footer-logo" src="LOGO_DATA_URI_PLACEHOLDER" alt="logo">
    <span class="footer-name">AI炼丹师</span>
    <span class="footer-right"><!-- 可选：日期或来源 --></span>
  </div>

</div>
</body>
</html>
```

> **关键约束**：
> - `footer-logo` 的 `src` 固定为仓库内 base64 data URI（见 `assets/logo_base64.txt`），**不得**替换为本地路径
> - `footer-name` 固定为 `AI炼丹师`
> - 生成 HTML 时，将 `LOGO_DATA_URI_PLACEHOLDER` 替换为 `assets/logo_base64.txt` 的完整内容

### 2a. 读取 logo base64（替换 placeholder）

```python
import os

# 读取 base64（相对于 skill 目录，或用绝对路径）
skill_dir = os.path.dirname(os.path.abspath(__file__))  # 调整为实际路径
logo_b64_path = os.path.join(skill_dir, "assets", "logo_base64.txt")
with open(logo_b64_path) as f:
    logo_data_uri = f.read().strip()

# 替换 HTML 模板中的占位符
html = html_template.replace("LOGO_DATA_URI_PLACEHOLDER", logo_data_uri)
```

或者直接在生成卡片时内联读取：

```bash
LOGO_B64=$(cat skills/plain-card/assets/logo_base64.txt)
# 然后在 Python/Node 脚本中读取该文件注入 HTML
```

### 3. 截图脚本

保存到 `/tmp/screenshot_plain_card.js`：

```javascript
const puppeteer = require('puppeteer');  // GitHub Actions: npm install puppeteer
// 本地 macOS: require('/opt/homebrew/lib/node_modules/puppeteer')

(async () => {
  const browser = await puppeteer.launch({
    headless: 'new',
    args: ['--no-sandbox', '--disable-setuid-sandbox']
    // 无需 --allow-file-access-from-files，因为 logo 已内嵌为 data URI
  });
  const page = await browser.newPage();
  const filePath = 'file:///tmp/<name>_plain_card.html';   // ← 替换文件名

  await page.goto(filePath, { waitUntil: 'networkidle0' });

  const dim = await page.evaluate(() => {
    const r = document.querySelector('.card').getBoundingClientRect();
    return { width: r.width, height: r.height };
  });
  await page.setViewport({
    width: Math.ceil(dim.width) + 64,
    height: Math.ceil(dim.height) + 64,
    deviceScaleFactor: 2
  });

  await page.goto(filePath, { waitUntil: 'networkidle0' });

  const card = await page.$('.card');
  await card.screenshot({ path: '/tmp/<name>_plain_card.png' });  // ← 替换文件名

  console.log('Done: /tmp/<name>_plain_card.png');
  await browser.close();
})();
```

运行：
```bash
node /tmp/screenshot_plain_card.js
```

### 4. 输出图片

用 `MEDIA:/tmp/<name>_plain_card.png` 发送给用户。

---

## 注意事项

- **Footer 不可定制**：logo 和名字 `AI炼丹师` 固定，不随用户要求变化
- **Logo 为 base64 内嵌**：无需本地文件路径，CI/CD 环境直接可用
- **logo_base64.txt 位置**：`skills/plain-card/assets/logo_base64.txt`（已提交到 git）
- **Puppeteer 路径**：本地 macOS 用 `/opt/homebrew/lib/node_modules/puppeteer`；GitHub Actions 用 `npm install puppeteer` 后直接 `require('puppeteer')`
- **中文字体**：`-apple-system, "PingFang SC"` 自动支持中文；GitHub Actions 需安装 `fonts-noto-cjk`
- **卡片宽度固定 600px**，高度自适应，2x 截图后实际 1200px 宽

---

## 参考文件

- `references/card-example.md`：完整卡片 HTML 示例（含 base64 logo）
- `references/card-format.md`：格式规范与组件库
- `assets/logo.png`：logo 原始文件（64×64 PNG）
- `assets/logo_base64.txt`：logo 的 base64 data URI（在 HTML 中直接使用）
