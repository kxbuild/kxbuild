# 用 kxbuild 构建 Flutter 工程的 iOS 应用

[English](../en/flutter.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上构建 Flutter 工程的 iOS 端。用它代替 `flutter build ios`，其他 `flutter` 命令照常使用。Runner 工程里的 Flutter 构建脚本会调用 `xcodebuild` 和 `xcrun`，这些命令由 kxbuild 提供，所以工程无需修改即可构建。

## 步骤

### 1. 安装 Flutter

按 Flutter 官网说明安装 Flutter SDK，并把 `flutter` 命令加入 `PATH`。新开一个终端，下面的命令能输出版本号即可。

```shell
flutter --version
```

### 2. 创建工程

已有工程可跳过。否则用 `flutter create` 创建，或使用 kxbuild 模板（它会执行 `flutter create` 并设置 Runner Target 的 Bundle ID）：

```shell
kxbuild project create -t flutter_app -n myflutterapp -b com.example.myflutterapp -o D:\work
```

### 3. 对工程运行 doctor

首次构建某个工程前执行。doctor 会检查 Flutter 环境并准备好 iOS 工程。

```shell
kxbuild doctor D:\work\myflutterapp
```

如果工程用到需要 CocoaPods 的插件，执行完 doctor 后在 `ios` 目录安装依赖，见 [CocoaPods](cocoapods.md)；没有则跳过。

```shell
cd ios
pod install
```

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone。Flutter 的 Debug 构建必须连着调试器才能运行，所以要在手机上试用时，请用 Release 构建并安装：

```shell
kxbuild build .\ios\Runner.xcworkspace -c Release --install
```

### 5. 发布

用 Release 配置构建，在 `ios` 目录下得到 `build\Runner.ipa`，可直接上传 App Store Connect。

```shell
kxbuild build .\ios\Runner.xcworkspace -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| Run Script 阶段失败，例如没有设置 `FLUTTER_ROOT` | 带工程路径执行 `kxbuild doctor <工程目录>`，它会配置好 Flutter 脚本需要的环境 |
| 构建时找不到插件的头文件或模块 | 在 `ios` 目录执行 `pod install`，然后构建 `Runner.xcworkspace` 而不是 `Runner.xcodeproj` |
