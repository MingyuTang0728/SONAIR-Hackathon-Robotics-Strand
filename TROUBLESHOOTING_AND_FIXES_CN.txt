# SONAIR Hackathon 问题记录与现场排错指南

本文档总结了主办方预演 UCL 参赛者 Sub-portal 接入流程时出现的问题、原因、解决方法，以及黑客松现场最可能遇到的错误。

## 一、完整目标流程

1. 主办方启动 `organiser/sonair_application_server.py`。
2. 主办方打开 `organiser/SONAIR_main_portal_v17.html`。
3. 参赛者基于 `participant_template` 设计自己的 Sub-portal。
4. 参赛者部署 GitHub Pages，并提供 `federation.json` 地址。
5. 主 portal 读取 `federation.json`，把参赛者节点加入 UK map / Co-Creation Space。
6. 访问者在主 portal 填写申请。
7. 主 portal POST 到主办方申请服务器。
8. 参赛者 Sub-portal inbox 通过 `subportal_id` 收到申请。
9. 参赛者点击 Approve 或 Reject。
10. Main portal 轮询到 approved 后显示 `Open approved sub-portal`。
11. 访问者打开被批准的 Sub-portal 页面。

## 二、本次预演出现的问题与解决方法

### 问题 1：服务器只绑定 127.0.0.1，参赛者电脑无法访问

现象：主办方电脑本机可以访问 API，但参赛者电脑访问 `http://主办方IP:5055` 失败。

原因：Python server 如果只监听 `127.0.0.1`，只能本机访问。

解决：`sonair_application_server.py` 必须监听：

```python
ThreadingHTTPServer(('0.0.0.0', port), Handler).serve_forever()
```

现场检查：终端应显示类似：

```text
Network mode enabled: share http://YOUR_LAPTOP_IP:5055 with participant sub-portals
```

### 问题 2：Sub-portal GitHub 仓库缺少 `sonair_request_system.js`

现象：本地打开 Sub-portal 可以显示 request inbox，但 GitHub Pages 打开后 inbox 区域为空。

原因：`index.html` 引用了：

```html
<script src="sonair_request_system.js"></script>
```

但 GitHub 仓库里没有上传这个文件。

解决：参赛者 GitHub 仓库根目录必须包含：

```text
index.html
federation.json
theme.config.json
sonair_request_system.js
```

现场检查：直接打开：

```text
https://用户名.github.io/仓库名/sonair_request_system.js
```

如果看到 JS 代码，说明文件存在；如果 404，说明缺文件。

### 问题 3：Main portal 显示 `Application ID: local-...`

现象：主 portal 显示申请提交成功，但 Application ID 是：

```text
local-1781008989557
```

原因：Main portal 没有成功 POST 到申请服务器，而是进入了本地 fallback。Sub-portal 查的是服务器数据库，所以收不到这条申请。

正确现象：Application ID 应该是 UUID，例如：

```text
717b3682-c648-4070-8f57-97e0b990dd9c
```

解决：确保 `SONAIR_main_portal_v17.html` 中的 API 地址指向主办方电脑 IP：

```js
const SONAIR_API_BASE = 'http://主办方IP:5055';
```

或在 localStorage 中设置同样地址。

现场检查：PyCharm / Terminal 日志必须出现：

```text
POST /api/applications HTTP/1.1" 201
```

如果没有 POST，说明 Main portal 没有真正写入服务器。

### 问题 4：Main portal approved 后没有进入 Sub-portal 的按钮

现象：Sub-portal 已经 approve，Main portal 状态变成 approved，但没有 `Open approved sub-portal`。

原因：旧版 main portal 对 federated nodes 不轮询状态，或者 approved 后只显示文字，不显示打开入口。

解决：新版 `SONAIR_main_portal_v17.html` 已修复：

- Federated nodes 也会轮询申请状态。
- Approved 后弹窗中显示 `Open approved sub-portal`。
- 关闭弹窗后，项目卡片上也会显示 `Open approved sub-portal`。

### 问题 5：点击 `Open approved sub-portal` 后 404

现象：打开了：

```text
https://ucl-team.github.io/sonair-ucl-subportal/
```

但页面 404。

原因：`federation.json` 中仍然保留示例 URL，而不是参赛者真实 GitHub Pages URL。

解决：参赛者必须把 `federation.json` 里的 URL 全部改成自己的真实地址，例如：

```json
"url": "https://mingyutang0728.github.io/SONAIR-TEST/",
"visitor_url": "https://mingyutang0728.github.io/SONAIR-TEST/"
```

现场检查：直接打开：

```text
https://用户名.github.io/仓库名/federation.json
```

确认里面没有 `ucl-team.github.io`、`your-username`、`example` 等占位内容。

### 问题 6：GitHub Pages 是 HTTPS，本地 API 是 HTTP

现象：Sub-portal 部署在 GitHub Pages 后，请求 `http://192.168.x.x:5055` 可能被浏览器拦截。

原因：HTTPS 页面访问 HTTP API 可能触发 mixed content 或 private network request 限制。

临时解决：黑客松现场建议先用本地 HTTP 测试：

```bash
python3 -m http.server 8000
```

然后打开：

```text
http://localhost:8000
```

正式解决：给主办方 API 加 HTTPS tunnel，例如 ngrok / cloudflared，然后让参赛者使用：

```js
window.SONAIR_API_BASE = 'https://xxxxx.ngrok-free.app';
```

## 三、参赛者必须保持一致的 3 个 ID / URL

### 1. `window.SONAIR_NODE_ID`

在 Sub-portal 页面中：

```js
window.SONAIR_NODE_ID = 'ucl';
```

### 2. `federation.json -> node.id`

必须完全一致：

```json
"id": "ucl"
```

### 3. Main portal 收到的 project nodeId

Main portal 会根据 `federation.json` 自动生成。只要 `node.id` 正确，路由就正确。

如果一个写 `ucl`，一个写 `UCL`，另一个写 `ucl-node`，申请就会收不到。

## 四、现场快速排错表

| 现象 | 原因 | 解决 |
|---|---|---|
| Sub-portal 显示 No requests | 数据库里没有该 node 的申请 | 检查 Main portal 是否出现 `POST /api/applications 201` |
| Main portal 显示 `local-...` | 没有连上后端 | 检查 `SONAIR_API_BASE` 和服务器是否运行 |
| GitHub Pages inbox 不显示 | 缺少 `sonair_request_system.js` 或 HTTPS/HTTP 被拦截 | 上传 JS 文件；本地 HTTP 测试；或使用 HTTPS tunnel |
| Approve 后 Main portal 没反应 | Main portal 没轮询状态或页面没刷新新版 | 使用修正版 main portal，强制刷新 `Command + Shift + R` |
| Open approved sub-portal 404 | `federation.json` 里 URL 写错 | 改成真实 GitHub Pages URL |
| UCL 收不到 UCL 的申请 | `SONAIR_NODE_ID` 和 `node.id` 不一致 | 保证两个字符串完全一致 |
| 申请服务器能 GET 但不能 POST | CORS / API 地址 / 端口问题 | Console 测试 POST，检查终端日志 |

## 五、现场最小测试步骤

1. 主办方启动服务器：

```bash
cd organiser
python3 sonair_application_server.py
```

2. 主办方确认 IP：

```bash
ifconfig | grep "inet " | grep -v 127.0.0.1
```

3. 浏览器打开健康检查：

```text
http://主办方IP:5055/api/health
```

4. 参赛者配置：

```js
window.SONAIR_NODE_ID = 'your-node-id';
window.SONAIR_API_BASE = 'http://主办方IP:5055';
```

5. 主 portal 提交申请后检查终端：

```text
POST /api/applications HTTP/1.1" 201
```

6. Sub-portal approve 后检查终端：

```text
PATCH /api/applications/<id>/status HTTP/1.1" 200
```

7. Main portal 弹窗出现 `Open approved sub-portal`，点击后应进入真实 GitHub Pages URL。
