

## 推荐工作流（最稳、不抢剪贴板）

### 1) Electron 调 Snipaste 进入截图，并把结果直接输出到临时文件

Snipaste 支持：`snip ... -o FILE_NAME`（截图完成后保存到指定文件）。([GitHub][1])

示例命令（区域截图，由用户框选）：

```bat
"C:\Program Files\Snipaste\Snipaste.exe" snip -o "C:\Users\你\AppData\Local\你的App\tmp\cap.png"
```

你也可以做全屏/活动窗口：

* 全屏：`snip --full -o FILE_NAME` ([GitHub][1])
* 活动窗口：`snip --active-window -o FILE_NAME` ([GitHub][1])

> 选“输出文件”比 `-o clipboard` 更稳定：不会和用户自己的剪贴板内容冲突，也更容易判断“截图完成了没”。

### 2) Electron 监听这个文件生成/更新

* `fs.watch` 或轮询 `stat.mtime` 都行
* 一旦 cap.png 写入完成：读成 Buffer → base64

### 3) 调 UmiOCR 做 OCR（本地 HTTP）

把图片 base64 发给 UmiOCR 的 OCR 接口拿到文本（你之前想走 UmiOCR 这条就很顺）。

### 4) 把 OCR 文本发给 AI

在 **主进程**请求 AI（避免 key 暴露到渲染进程）。

### 5) 展示：一个 always-on-top 悬浮结果窗

Electron 窗口：

* `alwaysOnTop: true`
* `frame: false`, `transparent: true`
* 需要“点击穿透”就 `win.setIgnoreMouseEvents(true, { forward: true })`
