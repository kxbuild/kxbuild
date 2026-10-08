# 用 kxbuild 构建 Unity 导出的 iOS 工程

[English](../en/unity.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上构建 Unity 导出的 iOS 工程，完成签名并安装到 iPhone。照常从 Unity 导出，把导出目录当作普通的 Xcode 工程即可。不需要 Unity 插件，也不用修改 Unity 工程设置。

## 步骤

### 1. 从 Unity 导出 Xcode 工程

照常在 Unity 中开发。安装 iOS Build Support 模块后，在 Build Settings 中把平台切换为 iOS，构建到一个目录。

### 2. 运行 doctor

doctor 检查构建环境，并补齐缺少的部分。

```shell
kxbuild doctor .\Unity-iPhone.xcodeproj
```

如果用了带 iOS 依赖的插件（如 Firebase、Google Mobile Ads），导出目录里会有 Podfile，此时还要在导出目录执行 `pod install`，见 [CocoaPods](cocoapods.md)。

### 3. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone。Target 固定为 `Unity-iPhone`。

**没有 Podfile**

```shell
kxbuild build .\Unity-iPhone.xcodeproj -t Unity-iPhone --install
```

**有 Podfile，已执行 `pod install`**

```shell
kxbuild build .\Unity-iPhone.xcworkspace -t Unity-iPhone --install
```

### 4. 发布

用 Release 配置构建，.ipa 生成在导出目录下。

**没有 Podfile**

```shell
kxbuild build .\Unity-iPhone.xcodeproj -c Release -t Unity-iPhone
```

**有 Podfile，已执行 `pod install`**

```shell
kxbuild build .\Unity-iPhone.xcworkspace -c Release -t Unity-iPhone
```

修改 Unity 工程后，重新导出到同一目录再构建即可。

## 提示

- Windows 上请把导出目录放在 `D:\proj` 这样的短路径下，Unity 导出的目录层级很深。
