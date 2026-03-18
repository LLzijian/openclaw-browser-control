---
name: browser-control
description: Control Chrome browser via Chrome DevTools Protocol (CDP). Use when user asks to operate browser, open webpage, search, get web content, click links, fill forms, or any browser-related tasks. Triggers on phrases like "操作浏览器", "打开网页", "搜索", "获取网页内容", "控制浏览器", "Chrome", "browser", "网页", "填表".
---

# 🦞 浏览器控制 Skill

通过 Chrome DevTools Protocol (CDP) 远程控制宿主机上的 Chrome 浏览器。

## ⚠️ 启动前须知

**必须先关闭所有 Chrome 窗口**，否则可能导致端口冲突（9222 被占用）。

```powershell
# 关闭所有 Chrome 进程
Stop-Process -Name "chrome" -Force -ErrorAction SilentlyContinue
```

## 🚀 启动命令（Windows PowerShell）

```powershell
& "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --remote-debugging-address=0.0.0.0 --remote-allow-origins="*" --user-data-dir="$env:TEMP\chrome_debug"
```

> 如果 Chrome 安装在非默认路径，请替换为实际路径。

## 🔧 核心 API

### HTTP 端点

**打开新页面：**
```bash
curl -s --noproxy '*' -X PUT -H "Host: localhost" \
  'http://host.docker.internal:9222/json/new?https://example.com'
```

**获取页面列表：**
```bash
curl -s --noproxy '*' -H "Host: localhost" \
  'http://host.docker.internal:9222/json'
```

### WebSocket 命令（CDP）

连接到页面：
```javascript
const pageId = 'PAGE_ID_FROM_/json';
const ws = new WebSocket(`ws://host.docker.internal:9222/devtools/page/${pageId}`);
```

**导航页面：**
```javascript
ws.send(JSON.stringify({ 
  id: 1, 
  method: 'Page.navigate', 
  params: { url: 'https://example.com' } 
}));
```

**执行 JavaScript：**
```javascript
ws.send(JSON.stringify({ 
  id: 2, 
  method: 'Runtime.evaluate', 
  params: { 
    expression: 'document.querySelector("button").click()',
    returnByValue: true 
  } 
}));
```

**输入文本：**
```javascript
ws.send(JSON.stringify({ 
  id: 3, 
  method: 'Input.insertText', 
  params: { text: 'Hello World' } 
}));
```

**模拟点击：**
```javascript
ws.send(JSON.stringify({ 
  id: 4, 
  method: 'Input.dispatchMouseEvent', 
  params: { 
    type: 'mousePressed', 
    x: 100, 
    y: 200, 
    button: 'left', 
    clickCount: 1 
  } 
}));
ws.send(JSON.stringify({ 
  id: 5, 
  method: 'Input.dispatchMouseEvent', 
  params: { 
    type: 'mouseReleased', 
    x: 100, 
    y: 200, 
    button: 'left', 
    clickCount: 1 
  } 
}));
```

## 📋 两阶段工作流

### 阶段一：请求宿主机唤醒

在执行浏览器任务前，输出以下提示：

> 🦞 小龙虾浏览器接管程序已就绪！
> 因为我住在 Docker 容器里，需要你帮我在宿主机上打开一扇门。请在你的 Windows PowerShell 中直接复制并回车执行以下命令，召唤一个纯净的 Chrome 分身：
> 
> ```powershell
> & "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --remote-debugging-address=0.0.0.0 --remote-allow-origins="*" --user-data-dir="$env:TEMP\chrome_debug"
> ```
> 
> 只要干净的浏览器一弹出来，请直接回复我："已启动"。

**【强制动作】**：输出后立即停止，等待用户回复"已启动"。

### 阶段二：接管与执行

1. 确认连接：`curl http://host.docker.internal:9222/json`
2. 执行用户任务（导航、点击、提取数据等）
3. 完成后提醒用户关闭调试窗口以确保安全

## 🔒 安全提示

测试完毕后，直接关闭那个 Chrome 调试窗口即可切断连接。
