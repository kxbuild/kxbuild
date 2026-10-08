# 用 kxbuild 构建 Cordova iOS 应用

[English](../en/cordova.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上打包 Cordova 工程的 iOS 应用。`cordova platform add ios` 生成的 `platforms/ios` 工程像普通 Xcode 工程一样构建，`cordova` 命令和插件照常使用。

## 步骤

### 1. 创建 Cordova 工程并添加 iOS 平台

已有工程在工程目录添加 iOS 平台即可。否则用 npm 安装 Cordova，创建工程，再在工程目录中添加 iOS 平台，Xcode 工程会生成在 `platforms/ios` 下。

```shell
npm install -g cordova
cordova create MyApp com.example.myapp MyApp
cd MyApp
cordova platform add ios
```

### 2. 运行 doctor

doctor 检查构建环境并准备好 iOS 工程。

```shell
kxbuild doctor .\platforms\ios\App.xcworkspace
```

工程默认用 Swift Package 管理依赖，不需要 CocoaPods。如果添加的插件带有 CocoaPods 依赖，`platforms/ios` 下会出现 Podfile。Windows 上 Cordova 不会自动安装这些 pod，请在 `platforms/ios` 执行一次 `pod install`，以后增删此类插件时再执行一次，见 [CocoaPods](cocoapods.md)。

```shell
cd platforms\ios
pod install
```

### 3. 同步 Web 代码

继续在 `www/` 下编写 Web 代码，修改后执行下面的命令同步到 iOS 工程。

```shell
cordova prepare ios
```

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后执行：

```shell
kxbuild build .\platforms\ios\App.xcworkspace --install
```

### 5. 发布

用 Release 配置构建，.ipa 生成在 `platforms/ios` 下。

```shell
kxbuild build .\platforms\ios\App.xcworkspace -c Release
```
