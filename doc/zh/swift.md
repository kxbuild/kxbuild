# 用 kxbuild 构建 Swift iOS 应用

[English](../en/swift.md) · **简体中文**

kxbuild 在 Windows、Linux、macOS 上构建 Swift iOS 工程，完成签名并安装到 iPhone。支持 SwiftUI、UIKit、Swift Package Manager 依赖以及 Swift / Objective-C 混编。编译设置、Target 依赖和编译参数都按 Xcode 的规则读取，工程无需修改即可构建。

Swift 工程分两类：[Xcode 工程](#xcode)和只有 Package.swift 的[独立 Swift Package](#package)，下面分别说明。使用 CocoaPods 的工程见 [CocoaPods](cocoapods.md)。

<a id="xcode"></a>

## Xcode 工程

带 .xcodeproj 或 .xcworkspace 的工程。Swift Package 依赖会在每次构建前自动解析。

### 1. 创建工程

已有工程可跳过。否则用 SwiftUI（`app_swiftui`）或 UIKit（`app_swift`）模板创建：

```shell
kxbuild project create -t app_swiftui -n MyApp -b com.example.myapp -o D:\projects
```

### 2. 运行 doctor

doctor 检查构建环境，并补齐缺少的部分。

```shell
kxbuild doctor .\MyApp.xcodeproj
```

<a id="add-package"></a>

### 3. 添加 Swift Package 依赖（可选）

相当于 Xcode 的 *Add Package Dependencies*。与 Xcode 一样，远程包默认使用最新版本，并允许升级到下一个主版本之前；添加时需要联网。

**远程包**

```shell
kxbuild project add-package -P .\MyApp.xcodeproj --url https://github.com/Alamofire/Alamofire.git --product Alamofire
```

**本地包**

```shell
kxbuild project add-package -P .\MyApp.xcodeproj --path ..\MyKit
```

版本规则：`--from`、`--exact`、`--up-to-next-minor`、`--range`、`--branch`、`--revision`。用 `-t` 指定链接这些产品的 Target。全部参数见 `kxbuild project add-package --help`。

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后以 Debug 配置构建并安装：

```shell
kxbuild build .\MyApp.xcodeproj --install
```

### 5. 发布

用 Release 配置构建，在工程目录下得到 `build\MyApp.ipa`，可直接上传 App Store Connect。

**.xcodeproj 工程**

```shell
kxbuild build .\MyApp.xcodeproj -c Release
```

**.xcworkspace 工作区**

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

<a id="package"></a>

## 独立 Swift Package

只有 Package.swift 的 Swift Package 本身不能打包成 App，在 Mac 上也是如此。

- **这个包就是一个 App**：它的可执行 Target 里有 SwiftUI 或 UIKit 的 App 入口。按下面的步骤把它转换成 App 工程。
- **这个包只提供库**给 App 使用：按上面[第 3 步](#add-package)把它作为依赖加入 App 工程。

### 1. 编译检查

在 Package.swift 所在目录执行：

```shell
kxbuild build .
```

### 2. 转换为 App 工程

转换会在包目录下新建 `MyApp` 子目录，里面是 App 工程。App 仍然使用包里的代码，修改包就是修改 App。请把这个子目录和包一起提交到版本库。

```shell
kxbuild project spm2app . -n MyApp -b com.example.myapp
```

如果 App 用到同一个包里没有对应 library 产品的 Target，转换时会在 Package.swift 的 `products` 中添加一条 `.library`。**请检查并提交这处修改。** 包里有多个 App 可执行 Target 时，用 `--executable` 指定。

### 3. 构建与发布

之后就是普通的 Xcode 工程，.ipa 生成在 `MyApp\build\MyApp.ipa`。

```shell
kxbuild build .\MyApp\MyApp.xcodeproj -c Release
```

## 常见问题

| 现象 | 解决办法 |
|---|---|
| Debug 构建链接失败：`undefined symbol: …vpfi` | 开源 Swift 编译器的已知问题。当属性带初始值（如 `@State private var count = 0`）且该 struct 的 init 写在另一个文件里时出现。设置环境变量 `KXBUILD_SWIFT_WHOLE_MODULE=all`（或单次构建加 `-D KXBUILD_SWIFT_WHOLE_MODULE=all`）后重新构建，Swift Target 将按整模块编译。 |
| Swift Package 下载不了，或构建时找不到包 | 执行 `kxbuild resolve .\MyApp.xcodeproj`。仍不行时加 `--force` 重置包缓存。 |
| `scheme / target "…" not found` | 执行 `kxbuild project info .\MyApp.xcodeproj` 列出全部 Scheme 和 Target。 |
