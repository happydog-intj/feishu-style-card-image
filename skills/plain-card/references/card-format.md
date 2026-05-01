# 简单卡片格式规范

## 卡片结构

```
.card
├── .card-header        ← 渐变背景，图标 + 标题 + 副标题
├── .card-body          ← 内容区（多个 .section 组成）
│   ├── .section        ← 每个内容块
│   │   ├── .section-title   ← 小节标题（带分割线）
│   │   └── 内容组件         ← desc-block / point-list / feature-grid 等
│   └── ...
└── .card-footer        ← 固定签名区（logo + AI炼丹师）
```

---

## 固定约束（必须遵守）

| 属性 | 值 | 说明 |
|------|----|------|
| footer logo src | `assets/logo_base64.txt` 的完整内容 | base64 data URI，已提交到 git，CI/CD 可用 |
| footer 名字 | `AI炼丹师` | 不可根据用户输入修改 |
| 卡片宽度 | `600px` | 固定，高度自适应 |
| 截图倍率 | `deviceScaleFactor: 2` | 输出 1200px 宽高清图 |

> **重要**：logo 以 base64 data URI 形式内嵌到 HTML 的 `<img src="...">` 中，
> 不依赖任何本地文件路径，GitHub Actions 环境无需额外配置即可渲染。

---

## Header 颜色方案

根据内容选择合适的渐变色：

| 主题 | gradient |
|------|----------|
| 蓝色（默认） | `135deg, #1456F0 0%, #1890FF 100%` |
| 紫色 | `135deg, #722ed1 0%, #9254de 100%` |
| 青绿 | `135deg, #0ea5e9 0%, #06b6d4 100%` |
| 橙红 | `135deg, #c0392b 0%, #e74c3c 100%` |
| 暗金 | `135deg, #b7791f 0%, #d69e2e 100%` |

---

## 内容组件

### desc-block（描述块，带左边框）
```css
.desc-block {
  background: #f7f8fc;
  border-left: 3px solid #1456F0;
  border-radius: 0 8px 8px 0;
  padding: 12px 14px;
  font-size: 13.5px; color: #333; line-height: 1.75;
}
```

### point-list（要点列表）
```css
.point-list { list-style: none; padding: 0; }
.point-list li {
  display: flex; gap: 8px; align-items: flex-start;
  font-size: 13.5px; color: #333; line-height: 1.6;
  padding: 6px 0; border-bottom: 1px solid #f5f5f5;
}
.point-list li::before { content: '▸'; color: #1456F0; flex-shrink: 0; }
```

### feature-grid（双列功能网格）
```css
.feature-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
.feature-item {
  background: #f7f8fc; border-radius: 8px;
  padding: 12px 14px; border: 1px solid #eaecf3;
}
```

### stat-row（数据行）
```css
.stat-row {
  display: flex; justify-content: space-around;
  padding: 12px 0; border-bottom: 1px solid #f0f0f0;
}
.stat-item { text-align: center; }
.stat-value { font-size: 24px; font-weight: 700; color: #1456F0; }
.stat-label { font-size: 12px; color: #888; margin-top: 2px; }
```

---

## Footer（固定结构，复制粘贴，不得修改）

读取 base64（Python）：
```python
with open("skills/plain-card/assets/logo_base64.txt") as f:
    logo_data_uri = f.read().strip()
html = html_template.replace("LOGO_SRC", logo_data_uri)
```

HTML 模板（`LOGO_SRC` 在写文件前替换为实际 data URI）：
```html
<div class="card-footer">
  <img class="footer-logo" src="LOGO_SRC" alt="logo">
  <span class="footer-name">AI炼丹师</span>
  <span class="footer-right"><!-- 可选：日期、来源等 --></span>
</div>
```

```css
.card-footer {
  background: #f7f8fc; border-top: 1px solid #eaecf3;
  padding: 11px 20px; display: flex; align-items: center; gap: 10px;
}
.footer-logo { width: 28px; height: 28px; border-radius: 50%; object-fit: cover; flex-shrink: 0; }
.footer-name { font-size: 13px; font-weight: 600; color: #333; }
.footer-right { margin-left: auto; font-size: 11.5px; color: #999; }
```

---

## 色彩系统

| Token | 值 | 用途 |
|-------|-----|------|
| `--blue-primary` | `#1456F0` | 主色、强调、边框 |
| `--bg-card` | `#ffffff` | 卡片背景 |
| `--bg-section` | `#f7f8fc` | 内容块背景 |
| `--bg-page` | `#f0f2f5` | 页面背景 |
| `--text-primary` | `#222` | 标题、重要文字 |
| `--text-body` | `#333` | 正文 |
| `--text-muted` | `#888` | 辅助、标签 |
| `--border` | `#eaecf3` | 边框分割线 |

---

## 截图脚本（Puppeteer）

```javascript
const puppeteer = require('/opt/homebrew/lib/node_modules/puppeteer');
(async () => {
  const browser = await puppeteer.launch({
    headless: 'new',
    args: ['--no-sandbox', '--disable-setuid-sandbox', '--allow-file-access-from-files']
  });
  const page = await browser.newPage();
  const filePath = 'file:///tmp/CARD_NAME_plain_card.html';

  await page.goto(filePath, { waitUntil: 'networkidle0' });
  const dim = await page.evaluate(() => {
    const r = document.querySelector('.card').getBoundingClientRect();
    return { width: r.width, height: r.height };
  });
  await page.setViewport({ width: Math.ceil(dim.width) + 64, height: Math.ceil(dim.height) + 64, deviceScaleFactor: 2 });
  await page.goto(filePath, { waitUntil: 'networkidle0' });

  const card = await page.$('.card');
  await card.screenshot({ path: '/tmp/CARD_NAME_plain_card.png' });
  console.log('Done');
  await browser.close();
})();
```

> **注意**：启动参数加 `--allow-file-access-from-files`，确保 Puppeteer 能读取本地 logo 图片。
