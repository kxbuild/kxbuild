# 让 App 通过 USB 访问电脑上的开发服务器

[English](../en/dev-server.md) · **简体中文**

很多框架的 Debug 版 App 在运行时从电脑上的开发服务器加载代码：React Native 和 Expo 的 Metro、Cordova / Ionic 的 livereload 等。App 要连上这个服务器，手机和电脑通常得在同一个网络，防火墙要放行端口；在 WSL 里电脑的地址还是 WSL 内部地址，手机根本连不到。

kxbuild 把这条路改走 USB：Debug 真机包里带一个小组件，在手机的 `127.0.0.1` 上监听开发服务器的端口；`kxdevice reverse` 把这些连接通过数据线接到电脑上对应的端口。`kxbuild run` 和 VS Code 插件会自动启动 `kxdevice reverse` 和开发服务器。手机和电脑不需要在同一个网络，也不用改防火墙。连不上 USB 时，组件退回到编译时记录的电脑局域网地址，和以前一样。

React Native 和 Expo 工程不用配置，kxbuild 认得 Metro。其他框架按下面三步接入。

## 1. 告诉 kxbuild 端口和启动命令

在工程的构建设置里（xcconfig 文件、工程设置，或命令行的 `-D`）加：

| 设置 | 作用 | 示例 |
|---|---|---|
| `KXBUILD_DEV_SERVER_PORTS` | App 要访问的端口，多个用逗号隔开，最多 8 个 | `8100,35729` |
| `KXBUILD_DEV_SERVER_COMMAND` | 启动开发服务器的命令。不设时 kxbuild 不启动，只提示你自己启动 | `ionic serve --livereload --port 8100` |
| `KXBUILD_DEV_SERVER_DIR` | 命令的工作目录，默认是工程所在目录 | `$(SRCROOT)/..` |
| `KXBUILD_DEV_SERVER_OVER_USB` | 设为 `NO` 关闭这个功能 | |

只在 Debug 配置、真机包上生效，Release 包不受影响。

## 2. 把 App 里的开发服务器地址改成 127.0.0.1

框架从哪里读开发服务器的地址，就把那里改成 `127.0.0.1`。例如 Cordova 的 `config.xml`：

```xml
<content src="http://127.0.0.1:8100/" />
```

React Native 和 Expo 的 `ip.txt` 由 kxbuild 自动改。

## 3. 运行

```shell
kxbuild run .\ios\MyApp.xcworkspace
```

kxbuild 构建、安装并启动 App，同时启动 `kxdevice reverse` 和开发服务器（端口上已有服务时直接复用）。在终端里运行时开发服务器留在当前终端，按 Ctrl-C 停止；加 `--json` 或在脚本、AI 工具里运行时，两者在后台继续运行，日志在 `~/.kxbuild/devserver/`，下一次 `run` 复用。`--no-dev-server` 跳过这一步。

在 VS Code 里按 F5 时插件做同样的事：开发服务器在名为 Dev Server（React Native 为 Metro）的终端里运行。

## 常见问题

| 问题 | 解决办法 |
|---|---|
| App 启动后提示连不上开发服务器 | 看 `kxbuild run` 的终端或 `~/.kxbuild/devserver/` 下的日志，确认开发服务器已启动、iPhone 用数据线连着电脑。服务器就绪后在 App 里重新加载 |
| 两个 Debug 版 App 来回切换时其中一个连不上 | 两个 App 都要用当前版本的 kxbuild 重新构建，旧版本的组件切换 App 时不会立刻让出端口 |
| WSL 里开发服务器很慢 | 把工程放在 Linux 主目录（`~/`）下，不要放在 `/mnt/` 下的 Windows 盘 |
