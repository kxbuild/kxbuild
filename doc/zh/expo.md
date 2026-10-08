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

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone。Debug 构建的 JS 从开发服务器加载，所以先在工程目录另开一个终端启动它：

```shell
npx expo start
```

然后构建并安装：

```shell
kxbuild build .\ios\expohello.xcworkspace --install
```

### 5. 发布

用 Release 配置构建，JS 会打包进 App，.ipa 生成在 `ios` 目录下。

```shell
kxbuild build .\ios\expohello.xcworkspace -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| App 启动后红屏 | iPhone 连不上开发服务器。让 iPhone 和电脑处于同一网络，并在防火墙中放行端口 |
| 没有 `ios` 目录 | 带工程路径执行 `kxbuild doctor <工程目录>`，它会生成 iOS 工程 |
