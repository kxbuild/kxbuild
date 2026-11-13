# 用 kxbuild 构建 React Native 工程的 iOS 应用

[English](../en/react-native.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上构建 React Native 工程的 iOS 端。JS 部分照常用 Metro。工程里调用 `xcodebuild` 或 `xcrun` 的构建脚本无需修改，因为 kxbuild 提供了同名命令，见[兼容 xcodebuild](../../README_zh.md#-兼容-xcodebuild)。

## 步骤

### 1. 安装 Node.js

按 React Native 官网说明安装 Node.js。新开一个终端，下面的命令能输出版本号即可。

```shell
node --version
```

### 2. 创建工程

已有工程可跳过。否则用模板创建（使用 React Native Community CLI 初始化）。父目录路径尽量短，依赖目录层级很深。

```shell
kxbuild project create -t react_native_app -n rnhello -b com.example.rnhello -o D:\work
```

### 3. 对工程运行 doctor

首次构建某个工程前执行。doctor 会检查 React Native 环境并准备好 iOS 工程。

```shell
kxbuild doctor D:\work\rnhello
```

执行完 doctor 后，在 `ios` 目录安装原生依赖，见 [CocoaPods](cocoapods.md)。

```shell
cd ios
pod install
```

### 4. 在 iPhone 上运行

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，在工程目录执行：

```shell
kxbuild run .\ios\rnhello.xcworkspace
```

kxbuild 构建、安装并启动 App，同时在这个终端里启动 Metro。App 通过数据线从 Metro 加载 JS，手机和电脑不需要在同一个 Wi-Fi 下。Metro 已经在另一个终端运行时直接复用；按 Ctrl-C 停止。

在 VS Code 里装了 KXBuild 插件的话，选中设备按 F5 就是这一套：插件自动启动 Metro，App 启动后提示已连上。

### 5. 改代码立刻看效果

App 跑起来之后，保持 Metro 的终端（或 VS Code 窗口）开着，按改的东西分三种情况：

| 改了什么 | 要做的事 |
|---|---|
| JS / TypeScript、样式、图片 | 保存即可，Fast Refresh 几秒内更新到手机上，页面状态保留。没刷出来就在 Metro 终端按 `r`，或摇一摇手机点 Reload |
| `ios/` 下的 Swift / Objective-C，或 Info.plist | 重新执行 `kxbuild run`（VS Code 里重新按 F5） |
| 增删了带原生代码的 npm 包 | 先在 `ios` 目录 `pod install`，再重新执行 `kxbuild run` |

调试：

- JS 断点和日志：在 Metro 终端按 `j` 打开 React Native DevTools，在 Sources 面板打断点，Console 面板看 `console.log`。
- 原生断点：在 VS Code 里按 F5 运行，直接在 Swift / Objective-C 代码上打断点，和 JS 调试互不影响。

### 6. 发布

用 Release 配置构建，JS 会打包进 App，.ipa 生成在 `ios` 目录下。

```shell
kxbuild build .\ios\rnhello.xcworkspace -c Release
```

## 排查问题

| 问题 | 解决办法 |
|---|---|
| App 启动后红屏 | App 连不上 Metro。用 `kxbuild run` 启动 App，并保持它的终端开着；iPhone 用 USB 连接电脑。Metro 就绪后在 App 里点 Reload JS |
| WSL 中构建和加载 JS 很慢 | 把工程放在 Linux 主目录（`~/`）下，不要放在 `/mnt/` 下的 Windows 盘 |
