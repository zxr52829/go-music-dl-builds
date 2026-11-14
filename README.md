# go-music-dl GUI Builds

[go-music-dl](https://github.com/guohuiyuan/go-music-dl) 的 Windows GUI 构建产物。

**这是非官方构建**，源码来自上游项目，本仓库只提供编译好的可执行文件与构建说明。

## 下载

到 [Releases](../../releases/latest) 页面取对应文件。

| 文件 | 大小 | 运行时依赖 |
|---|---|---|
| `music-dl-native.exe` | ~20 MB | **无** |
| `music-dl-desktop-go.exe` | ~32 MB | Microsoft Edge WebView2 Runtime |

两个都是 64 位 Windows 单文件程序，不需要安装，双击即用。

## 两个版本的真正区别

这是本仓库存在的主要理由——同一份代码，两种渲染方式。

### `music-dl-native.exe`：原生绘制

界面由 [Gio](https://gioui.org/) 直接在窗口里绘制，**不加载任何网页**。

- 目标机器不需要预装 WebView2，Win10/11 全新安装即可运行
- 启动更快，内存占用更低
- 界面是重新设计的，与上游 Web 版视觉不同（深色主题）

### `music-dl-desktop-go.exe`：WebView2 外壳

进程内启动一个本地 HTTP 服务，再用系统 WebView2 加载它的页面。

- 界面与浏览器访问 `http://localhost:8080/music` 完全一致
- **必须在目标机器安装 [WebView2 Runtime](https://developer.microsoft.com/microsoft-edge/webview2/)**，Windows 11 通常自带，Windows 10 可能需要手动安装
- 功能与上游 Web 版保持同步

两者共用同一套 `core` 包，搜索、下载、音源行为一致，差别只在界面渲染。

## 界面预览

原生版界面包含三个页面：

- **搜索** —— 多音源并发搜索，显示歌曲名、歌手、专辑、音源、时长，支持单曲/歌单切换
- **下载** —— 下载任务列表与状态（等待/下载中/完成/失败/已存在）
- **音源** —— 当前可用的全部音源列表

## 功能

- 聚合搜索：网易云、QQ音乐、酷狗、酷我、咪咕、千千、汽水、5sing、Bilibili 等
- 并发请求：搜索时同时查询前 4 个音源
- 下载：自动内嵌封面、歌词（ID3 标签）
- 支持无损音质（取决于音源）

## 已知限制

- 原生版的界面功能是上游 Web 版的**子集**。歌单详情、排行榜、本地音乐管理、播放器、设置等页面尚未在原生版实现。
- 需要完整功能请用 `music-dl-desktop-go.exe` 或直接用上游 CLI/Web 版。
- 部分音源需要配置 Cookie 才能获取高品质音源，原生版暂不支持在界面内配置，需手动编辑配置文件。

## 自行构建

需要 Go 1.25.1 以上。

### 原生版（无 WebView2）

```bash
go build -ldflags="-H windowsgui -w -s" -o music-dl-native.exe ./desktop_native
```

### WebView2 版

```bash
# 先生成 Windows 资源文件
go generate ./winres
go-winres make --in ./winres/desktop_go.winres.json --out ./desktop_go/rsrc

# 需要 CGO 和 gcc
set CGO_ENABLED=1
go build -ldflags="-H windowsgui -w -s" -o music-dl-desktop-go.exe ./desktop_go
```

### CLI / Web 版

```bash
set CGO_ENABLED=0
go build -ldflags="-s -w" -o music-dl.exe ./cmd/music-dl
```

## 许可证与来源

上游项目采用 **AGPL-3.0**，详见 [LICENSE](./LICENSE)。本仓库的二进制与源码包受同一许可证约束。

- 上游源码：<https://github.com/guohuiyuan/go-music-dl>
- 源码包：`source.zip` 附在 Releases 中，对应上述 commit
- 原生界面代码位于 `desktop_native/` 目录

AGPL-3.0 要求分发二进制时提供对应源码，因此每次 Release 都附带 `source.zip`。

## 免责

仅供个人学习与技术研究。下载内容的版权归各平台与权利人所有，请勿用于商业用途或二次分发。使用本工具产生的任何后果由使用者自行承担。
