# 用 kxbuild 构建 Expo 工程的 iOS 应用

[English](../en/expo.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 本地构建 Expo 工程的 iOS 端，不需要云端构建服务。工程没有 `ios/` 目录时由 doctor 生成。JS 部分照常运行 Expo 开发服务器。

## 步骤

### 1. 安装 Node.js

按 Expo 官网说明安装 Node.js。新开一个终端，下面的命令能输出版本号即可。

```shell
node --version
```

### 2. 创建工程

已有工程可跳过。否则用模板创建（依次执行 `create-expo-app`、`expo prebuild` 和 `pod install`）。父目录路径尽量短，依赖目录层级很深。

```shell
kxbuild project create -t expo_app -n expohello -b com.example.expohello -o D:\work
```

### 3. 对工程运行 doctor

首次构建某个工程前执行。doctor 会检查 Expo 环境，生成并准备好 iOS 工程。

```shell
kxbuild doctor D:\work\expohello
```

执行完 doctor 后，在 `ios` 目录安装原生依赖，见 [CocoaPods](cocoapods.md)。

```shell
cd ios
pod install
```

### 4. 在 iPhone 上运行

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，在工程目录执行：

```shell
kxbuild run .\ios\expohello.xcworkspace
```

kxbuild 构建、安装并启动 App，同时在这个终端里启动 Expo 开发服务器（`npx expo start`）。App 通过数据线从它加载 JS，手机和电脑不需要在同一个 Wi-Fi 下。开发服务器已经在另一个终端运行时直接复用；按 Ctrl-C 停止。

在 VS Code 里装了 KXBuild 插件的话，选中设备按 F5 就是这一套：插件自动启动开发服务器，App 启动后提示已连上。

### 5. 改代码立刻看效果

App 跑起来之后，保持开发服务器的终端（或 VS Code 窗口）开着，按改的东西分三种情况：

| 改了什么 | 要做的事 |
|---|---|
| JS / TypeScript、样式、图片、路由文件 | 保存即可，Fast Refresh 几秒内更新到手机上，页面状态保留。没刷出来就在终端按 `r`，或摇一摇手机点 Reload |
| `app.json`、`app.config.js`，或装了带原生代码的 Expo 模块 / npm 包 | 删掉 `ios` 目录，重新执行 `kxbuild doctor` 生成，在 `ios` 目录 `pod install`，再执行 `kxbuild run` |
| `ios/` 下的原生代码 | 重新执行 `kxbuild run`（VS Code 里重新按 F5）。注意下次重新生成 `ios` 目录会覆盖手改的内容 |

调试：

- JS 断点和日志：在终端按 `j` 打开 Expo 的调试器，在 Sources 面板打断点，Console 面板看 `console.log`。
- 原生断点：在 VS Code 里按 F5 运行，直接在 `ios/` 下的 Swift / Objective-C 代码上打断点，和 JS 调试互不影响。

### 6. 发布

用 Release 配置构建，JS 会打包进 App，.ipa 生成在 `ios` 目录下。

```shell
kxbuild build .\ios\expohello.xcworkspace -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| App 启动后红屏 | App 连不上开发服务器。用 `kxbuild run` 启动 App，并保持它的终端开着；iPhone 用 USB 连接电脑。开发服务器就绪后在 App 里点 Reload JS |
| WSL 中构建失败，提示扫描 Expo 模块未完成，或加载 JS 很慢 | 把工程放在 Linux 主目录（`~/`）下，不要放在 `/mnt/` 下的 Windows 盘 |
| 没有 `ios` 目录 | 带工程路径执行 `kxbuild doctor <工程目录>`，它会生成 iOS 工程 |
