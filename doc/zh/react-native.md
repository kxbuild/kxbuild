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

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone。Debug 构建的 JS 从 Metro 加载，所以先在工程目录另开一个终端启动 Metro：

```shell
npx react-native start
```

然后构建并安装：

```shell
kxbuild build .\ios\rnhello.xcworkspace --install
```

iPhone 和电脑需要在同一个网络，App 才能连上 Metro。

### 5. 发布

用 Release 配置构建，JS 会打包进 App，.ipa 生成在 `ios` 目录下。

```shell
kxbuild build .\ios\rnhello.xcworkspace -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| App 启动后红屏 | iPhone 连不上 Metro。让 iPhone 和电脑处于同一网络，并在防火墙中放行 Metro 端口 |
| Run Script 阶段失败 | 带工程路径执行 `kxbuild doctor <工程目录>`，它会安装 React Native 脚本需要的依赖 |
| Windows 上报路径错误 | 把工程放在 `D:\work` 这样的短路径下。doctor 会开启 Windows 长路径支持（需一次管理员授权） |
