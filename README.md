# 🦞 OpenClaw Browser Control Skill

[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/LLzijian/openclaw-browser-control)
[![Chrome DevTools Protocol](https://img.shields.io/badge/CDP-9222-green)](https://chromedevtools.github.io/devtools-protocol/)

**Docker 环境中的 OpenClaw 远程控制宿主机 Chrome 浏览器的完整解决方案**

让运行在 Docker 容器中的 OpenClaw 实例，能够跨越网络隔离，直接接管并操作 Windows/macOS/Linux 宿主机上的 Chrome 浏览器。

---

## ✨ 特性

- 🌐 **跨环境控制** - Docker 容器直接操作宿主机浏览器
- 🔧 **CDP 协议** - 基于 Chrome DevTools Protocol
- 📝 **自然语言指令** - 无需编写代码，直接对话即可
- 🔒 **安全可控** - 独立的调试 Chrome 实例，不影响日常浏览

---

## 🚀 快速开始

### 1. 启动 Chrome 调试模式

**Windows PowerShell:**
```powershell
# 关闭所有 Chrome 进程
taskkill /F /IM chrome.exe

# 启动调试模式
& "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --remote-debugging-address=0.0.0.0 --remote-allow-origins="*" --user-data-dir="$env:TEMP\chrome_debug"
```

**macOS:**
```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --remote-allow-origins="*" --user-data-dir="/tmp/chrome_debug"
```

**Linux:**
```bash
google-chrome --remote-debugging-port=9222 --remote-allow-origins="*" --user-data-dir="/tmp/chrome_debug"
```

### 2. 配置 OpenClaw

在 OpenClaw 配置文件中添加：

```json
{
  "browser": {
    "enabled": true,
    "attachOnly": true,
    "cdpUrl": "http://host.docker.internal:9222",
    "ssrfPolicy": {
      "dangerouslyAllowPrivateNetwork": true
    }
  }
}
```

### 3. 使用示例

直接对 OpenClaw 说：

> "帮我打开百度，搜索 Docker 教程"

> "在当前页面找到登录按钮并点击"

> "打开小红书，发一条帖子，内容是..."

---

## 📖 详细文档

### 核心 API

| 命令 | 用途 |
|------|------|
| `Page.navigate` | 导航到 URL |
| `Runtime.evaluate` | 执行 JavaScript |
| `Input.insertText` | 输入文本 |
| `Input.dispatchMouseEvent` | 模拟鼠标点击 |
| `Input.dispatchKeyEvent` | 模拟键盘按键 |

### HTTP 端点

```bash
# 打开新页面
curl -X PUT 'http://host.docker.internal:9222/json/new?https://example.com'

# 获取页面列表
curl 'http://host.docker.internal:9222/json'
```

---

## 🔧 故障排查

| 问题 | 解决方案 |
|------|----------|
| 连接超时 | 检查 `netstat -an \| findstr 9222` 确认端口监听 |
| 元素定位不准 | 增加等待时间，优化指令描述 |
| Token 不匹配 | 检查 `.env` 和 `openclaw.json` 中的 token 一致性 |

---

## 📄 文件说明

```
openclaw-browser-control/
├── SKILL.md        # Skill 定义文件
├── README.md       # 本文档
└── tutorial.md     # 详细教程
```

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

---

## 📜 License

MIT License

---

**🦞 Made with ❤️ by OpenClaw**
