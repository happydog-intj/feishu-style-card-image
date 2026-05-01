---
name: feishu-card
description: 将结构化信息（GitHub 库介绍、文章总结、产品简介等）渲染成飞书风格的卡片图片，输出高分辨率 PNG，可直接发送给用户。
triggers:
  - 帮我做成卡片图片
  - 飞书风格卡片
  - 生成信息卡片图片
  - 做成图片发给我
tools: [write_file, terminal, browser_navigate, browser_vision]
---

# 飞书风格卡片图片生成

## 概述

把结构化内容（GitHub 库介绍、文章总结、项目报告等）渲染为飞书风格的卡片 PNG 图片。
技术栈：**纯 HTML + CSS → Puppeteer 截图（2x 高清）**，无需外部图片服务。

---

## 步骤

### 1. 整理卡片内容

将信息归纳为以下区块（按需取舍）：
- **Header**：标题、副标题、图标 emoji
- **Repo/来源行**：链接或来源标注
- **Stats 徽章行**：Stars / Forks / 日期 等数值型信息
- **描述块**：带蓝色左边框的段落
- **安装/操作块**：深色背景的命令行
- **代码示例块**：语法高亮代码
- **功能特性**：双列网格卡片
- **Footer**：来源署名 + 链接

### 2. 编写 HTML 文件

保存到 `/tmp/<name>_card.html`，使用下方 CSS 设计系统：

**核心设计 Token：**
```css
/* 主色 */
--blue-primary: #1456F0;
--blue-light:   #f0f5ff;
--blue-border:  #d0e1fd;

/* 背景层次 */
--bg-card:    #ffffff;
--bg-section: #f7f8fc;
--bg-page:    #f0f2f5;

/* 文字 */
--text-primary:   #222;
--text-secondary: #333;
--text-muted:     #888;

/* 代码块（Catppuccin Mocha） */
--code-bg:  #1e1e2e;
--kw:  #cba6f7;   /* keyword - 紫 */
--fn:  #89b4fa;   /* function - 蓝 */
--st:  #a6e3a1;   /* string   - 绿 */
--cm:  #6c7086;   /* comment  - 灰 */
--nu:  #fab387;   /* number   - 橙 */
--ty:  #f9e2af;   /* type     - 黄 */
```

**关键组件样式：**

```css
/* 卡片容器 */
.card {
  background: #fff;
  border-radius: 12px;
  width: 600px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.10);
  overflow: hidden;
}

/* 蓝色渐变 Header */
.card-header {
  background: linear-gradient(135deg, #1456F0 0%, #1890FF 100%);
  padding: 22px 24px 20px;
  display: flex; align-items: center; gap: 14px;
}

/* Section 标题（带分割线） */
.section-title {
  font-size: 13px; font-weight: 700;
  color: #1456F0; letter-spacing: 0.5px; text-transform: uppercase;
  margin-bottom: 10px; display: flex; align-items: center; gap: 6px;
}
.section-title::after {
  content: ''; flex: 1; height: 1px; background: #e8edf5;
}

/* 描述块（蓝色左边框） */
.desc-block {
  background: #f7f8fc;
  border-left: 3px solid #1456F0;
  border-radius: 0 8px 8px 0;
  padding: 12px 14px;
  font-size: 13.5px; color: #333; line-height: 1.75;
}

/* Stats 徽章 */
.stat-badge {
  display: flex; align-items: center; gap: 5px;
  background: #f0f5ff; border: 1px solid #d0e1fd;
  border-radius: 20px; padding: 4px 12px;
  font-size: 12.5px; color: #2b4ea8; font-weight: 500;
}
/* 绿色变体 */ .stat-badge.green { background:#f0faf4; border-color:#b7e5c8; color:#1a7a40; }
/* 橙色变体 */ .stat-badge.orange { background:#fff8f0; border-color:#ffd6a0; color:#b05f00; }

/* 安装命令行 */
.install-block {
  background: #0d1117; border-radius: 8px;
  padding: 12px 16px; display: flex; align-items: center; gap: 10px;
}

/* 双列功能网格 */
.feature-list { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
.feature-item {
  display: flex; align-items: flex-start; gap: 8px;
  background: #f7f8fc; border-radius: 8px;
  padding: 10px 12px; border: 1px solid #eaecf3;
}

/* Footer */
.card-footer {
  background: #f7f8fc; border-top: 1px solid #eaecf3;
  padding: 12px 24px;
  display: flex; align-items: center; justify-content: space-between;
}
```

### 3. 截图脚本

保存到 `/tmp/screenshot_card.js`：

```javascript
const puppeteer = require('/opt/homebrew/lib/node_modules/puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    headless: 'new',
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  });
  const page = await browser.newPage();
  const filePath = 'file:///tmp/<name>_card.html';   // ← 替换文件名
  
  await page.goto(filePath, { waitUntil: 'networkidle0' });
  
  // 获取卡片尺寸，设置精确 viewport
  const dim = await page.evaluate(() => {
    const r = document.querySelector('.card').getBoundingClientRect();
    return { width: r.width, height: r.height };
  });
  await page.setViewport({
    width: Math.ceil(dim.width) + 64,
    height: Math.ceil(dim.height) + 64,
    deviceScaleFactor: 2        // 2x 高清
  });
  
  // 重新加载以应用 viewport
  await page.goto(filePath, { waitUntil: 'networkidle0' });
  
  const card = await page.$('.card');
  await card.screenshot({ path: '/tmp/<name>_card.png' });   // ← 替换文件名
  
  console.log('Done: /tmp/<name>_card.png');
  await browser.close();
})();
```

运行：
```bash
node /tmp/screenshot_card.js
```

### 4. 输出图片

用 `MEDIA:/tmp/<name>_card.png` 发送给用户。

---

## 注意事项

- **Puppeteer 路径**：`/opt/homebrew/lib/node_modules/puppeteer`（macOS Homebrew 安装）
- **中文字体**：macOS 系统字体 `-apple-system, "PingFang SC"` 自动支持中文，无需额外安装
- **卡片宽度固定 600px**，高度自适应内容，2x 截图后实际像素为 1200px 宽
- **代码语法高亮**：用 `<span class="kw/fn/st/cm">` 手动标注，无需引入 highlight.js
- **颜色变体**：Stats 徽章支持 `.blue`（默认）/ `.green` / `.orange` 三种
- **内容适配**：非 GitHub 库的内容（文章总结、产品介绍等）可去掉 Repo 行和 Stats 行，其余组件通用

---

## 参考案例

- **GitHub 库介绍**：Header + Repo行 + Stats徽章 + 描述块 + 安装命令 + 代码示例 + 功能网格 + Footer
- **文章总结**：Header + 描述块（核心观点）+ 功能网格（要点列表）+ Footer
- **工具对比**：Header + 描述块 + 表格（HTML `<table>`）+ Footer
