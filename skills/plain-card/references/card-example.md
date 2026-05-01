# 简单卡片示例

## 示例 1：知识总结卡

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
  .card-header {
    background: linear-gradient(135deg, #1456F0 0%, #1890FF 100%);
    padding: 22px 24px 20px;
    display: flex; align-items: center; gap: 14px;
  }
  .header-icon { font-size: 36px; }
  .header-text h1 { font-size: 20px; font-weight: 700; color: #fff; margin-bottom: 4px; }
  .header-text p { font-size: 12.5px; color: rgba(255,255,255,0.82); }
  .card-body { padding: 20px 24px 8px; }
  .section { margin-bottom: 18px; }
  .section-title {
    font-size: 11px; font-weight: 700; color: #1456F0;
    letter-spacing: 0.8px; text-transform: uppercase;
    margin-bottom: 10px; display: flex; align-items: center; gap: 6px;
  }
  .section-title::after { content: ''; flex: 1; height: 1px; background: #e8edf5; }
  .desc-block {
    background: #f7f8fc; border-left: 3px solid #1456F0;
    border-radius: 0 8px 8px 0;
    padding: 12px 14px; font-size: 13.5px; color: #333; line-height: 1.75;
  }
  .point-list { list-style: none; padding: 0; }
  .point-list li {
    display: flex; gap: 8px; align-items: flex-start;
    font-size: 13.5px; color: #333; line-height: 1.6;
    padding: 6px 0; border-bottom: 1px solid #f5f5f5;
  }
  .point-list li:last-child { border-bottom: none; }
  .point-list li::before { content: '▸'; color: #1456F0; flex-shrink: 0; margin-top: 1px; }
  .card-footer {
    background: #f7f8fc; border-top: 1px solid #eaecf3;
    padding: 11px 20px; display: flex; align-items: center; gap: 10px;
  }
  .footer-logo { width: 28px; height: 28px; border-radius: 50%; object-fit: cover; }
  .footer-name { font-size: 13px; font-weight: 600; color: #333; }
  .footer-right { margin-left: auto; font-size: 11.5px; color: #999; }
</style>
</head>
<body>
<div class="card">
  <div class="card-header">
    <div class="header-icon">🧠</div>
    <div class="header-text">
      <h1>Context Compaction 是什么</h1>
      <p>AI 对话中的上下文压缩机制</p>
    </div>
  </div>
  <div class="card-body">
    <div class="section">
      <div class="section-title">核心概念</div>
      <div class="desc-block">
        当对话 token 超过阈值时，AI 会自动压缩历史内容，
        保留摘要并继续对话。这是保证长对话不中断的关键机制。
      </div>
    </div>
    <div class="section">
      <div class="section-title">触发原因</div>
      <ul class="point-list">
        <li>System Prompt 每轮固定消耗 ~12,800 tokens</li>
        <li>默认阈值 50% 过低，32K 就触发压缩</li>
        <li>Shannon-auto 模型 context window 未正确识别</li>
      </ul>
    </div>
  </div>
  <div class="card-footer">
    <img class="footer-logo" src="/Users/a10093140/Desktop/me/logo.png" alt="logo">
    <span class="footer-name">AI炼丹师</span>
    <span class="footer-right">2025</span>
  </div>
</div>
</body>
</html>
```

---

## 示例 2：工具介绍卡

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: #f0f2f5; padding: 32px;
    font-family: -apple-system, "PingFang SC", "Helvetica Neue", sans-serif;
  }
  .card {
    background: #fff; border-radius: 12px; width: 600px;
    box-shadow: 0 2px 12px rgba(0,0,0,0.10); overflow: hidden;
  }
  .card-header {
    background: linear-gradient(135deg, #722ed1 0%, #9254de 100%);
    padding: 22px 24px 20px; display: flex; align-items: center; gap: 14px;
  }
  .header-icon { font-size: 36px; }
  .header-text h1 { font-size: 20px; font-weight: 700; color: #fff; margin-bottom: 4px; }
  .header-text p { font-size: 12.5px; color: rgba(255,255,255,0.82); }
  .card-body { padding: 20px 24px 8px; }
  .feature-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 18px; }
  .feature-item {
    background: #f7f8fc; border-radius: 8px;
    padding: 12px 14px; border: 1px solid #eaecf3;
  }
  .feature-item .icon { font-size: 20px; margin-bottom: 6px; }
  .feature-item .title { font-size: 13px; font-weight: 600; color: #222; margin-bottom: 3px; }
  .feature-item .desc { font-size: 12px; color: #666; line-height: 1.5; }
  .card-footer {
    background: #f7f8fc; border-top: 1px solid #eaecf3;
    padding: 11px 20px; display: flex; align-items: center; gap: 10px;
  }
  .footer-logo { width: 28px; height: 28px; border-radius: 50%; object-fit: cover; }
  .footer-name { font-size: 13px; font-weight: 600; color: #333; }
  .footer-right { margin-left: auto; font-size: 11.5px; color: #999; }
</style>
</head>
<body>
<div class="card">
  <div class="card-header">
    <div class="header-icon">🛠</div>
    <div class="header-text">
      <h1>Hermes Agent</h1>
      <p>多平台 AI 助手框架</p>
    </div>
  </div>
  <div class="card-body">
    <div class="feature-grid">
      <div class="feature-item">
        <div class="icon">🔌</div>
        <div class="title">多平台接入</div>
        <div class="desc">飞书、微信、Telegram、Discord 统一接入</div>
      </div>
      <div class="feature-item">
        <div class="icon">🧩</div>
        <div class="title">Skills 系统</div>
        <div class="desc">可插拔技能，按需加载，持续扩展</div>
      </div>
      <div class="feature-item">
        <div class="icon">💾</div>
        <div class="title">持久记忆</div>
        <div class="desc">跨会话记忆，用户偏好长期保留</div>
      </div>
      <div class="feature-item">
        <div class="icon">🔧</div>
        <div class="title">工具调用</div>
        <div class="desc">终端、浏览器、文件等 30+ 内置工具</div>
      </div>
    </div>
  </div>
  <div class="card-footer">
    <img class="footer-logo" src="/Users/a10093140/Desktop/me/logo.png" alt="logo">
    <span class="footer-name">AI炼丹师</span>
    <span class="footer-right">github.com/obra/superpowers</span>
  </div>
</div>
</body>
</html>
```
