<div align="center">

<img src="images/kxbuild-icon.svg" width="96" alt="kxbuild" />

# kxbuild

[English](README.md) · **简体中文**

**在 Windows、Linux、macOS 上用命令行构建 iOS App**

kxbuild 是面向 Xcode 工程的编译系统，作用相当于 Mac 上的 xcodebuild。<br/>
直接读取 .xcodeproj / .xcworkspace / Package.swift，兼容 xcodebuild 命令行语法，<br/>
产出可提交 App Store 的已签名 .ipa。支持 Windows、Linux、macOS，无需安装 Xcode。

<br/>

![Windows](https://img.shields.io/badge/Windows_10_/_11-0078D4?style=for-the-badge&logo=data:image/svg%2bxml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA4OCA4OCI+PHBhdGggZmlsbD0iI2ZmZiIgZD0iTTAgMTIuNCAzNS43IDcuNXYzNC41SDB6TTQwIDYuOSA4Ny4zIDB2NDEuOEg0MHpNMCA0NS43aDM1Ljd2MzQuNkwwIDc1LjN6TTQwIDQ1LjdoNDcuM1Y4OEw0MCA4MS4zeiIvPjwvc3ZnPgo=)
![Linux](https://img.shields.io/badge/Linux_/_WSL-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)

[安装](#-安装) · [工程类型](#-支持的工程类型) · [问题反馈](../../issues)

</div>

## ✨ 亮点

| | |
|---|---|
| 🖥️ **跨平台** | 在 Windows、Linux、macOS 上原生构建 |
| 📂 **工程零改动** | 按 Xcode 的规则读取编译设置、Scheme、Target 依赖和编译参数 |
| 🔧 **兼容 xcodebuild** | 自带同名的 `xcodebuild`、`xcrun`、`codesign`，各框架的构建脚本无需修改 |
| 🔏 **内置签名** | 把 .p12 和 .mobileprovision 放进 `keychain` 文件夹，每次构建自动签名 |
| 📦 **一条命令出 .ipa** | Release 构建直接生成可上传 App Store 的 .ipa，包含 SwiftSupport 和 Transporter 资源描述文件 |
| 📱 **装到真机** | 加 `--install` 一步完成构建、签名并安装到 USB 连接的 iPhone |
| 🤖 **适合脚本和 CI** | `--json` 每行输出一个 JSON 事件，退出码遵循 0 成功、非 0 失败 |
| ✍️ **任意编辑器** | 在终端、CI 流水线或任何你习惯的编辑器里使用，工程里不需要额外配置 |

## 🖥️ 支持平台

| 系统 | 版本 | 构建 | 签名 | 安装到 iPhone |
|:---:|:---:|:---:|:---:|:---:|
| <img src="images/windows.svg" width="18" /> **Windows** | Windows 10 / 11（推荐 WSL） | ✅ | ✅ | ✅ |
| <img src="https://cdn.simpleicons.org/linux/000000" width="18" /> **Linux** | 主流发行版 / WSL | ✅ | ✅ | ✅ |
| <img src="https://cdn.simpleicons.org/apple/000000" width="18" /> **macOS** | macOS | ✅ | ✅ | ✅ |

## 🧱 支持的工程类型

每篇文档介绍如何用 `kxbuild` 命令行准备并构建该类工程。

| 工程类型 | 文档 |
|---|---|
| <img src="https://cdn.simpleicons.org/swift/F05138" width="18" /> **Swift / SwiftUI / UIKit** 与 **Swift Package** | [doc/zh/swift.md](doc/zh/swift.md) |
| <img src="https://cdn.simpleicons.org/apple/438EFF" width="18" /> **Objective-C**（含 Objective-C / Swift 混编） | [doc/zh/objective-c.md](doc/zh/objective-c.md) |
| <img src="https://cdn.simpleicons.org/cocoapods/EE3322" width="18" /> **CocoaPods** | [doc/zh/cocoapods.md](doc/zh/cocoapods.md) |
| <img src="https://cdn.simpleicons.org/flutter/02569B" width="18" /> **Flutter** | [doc/zh/flutter.md](doc/zh/flutter.md) |
| <img src="https://cdn.simpleicons.org/react/61DAFB" width="18" /> **React Native** | [doc/zh/react-native.md](doc/zh/react-native.md) |
| <img src="https://cdn.simpleicons.org/expo/000020" width="18" /> **Expo** | [doc/zh/expo.md](doc/zh/expo.md) |
| 🟢 **uni-app（HBuilderX）** | [doc/zh/uni-app.md](doc/zh/uni-app.md) |
| <img src="https://cdn.simpleicons.org/kotlin/7F52FF" width="18" /> **Kotlin Multiplatform / Compose** | [doc/zh/kotlin-multiplatform.md](doc/zh/kotlin-multiplatform.md) |
| <img src="https://cdn.simpleicons.org/unity/000000" width="18" /> **Unity**（导出的 Xcode 工程） | [doc/zh/unity.md](doc/zh/unity.md) |
| <img src="https://cdn.simpleicons.org/godotengine/478CBF" width="18" /> **Godot**（导出的 Xcode 工程） | [doc/zh/godot.md](doc/zh/godot.md) |
| <img src="https://cdn.simpleicons.org/cocos/55C2E1" width="18" /> **cocos2d-x** | [doc/zh/cocos2d-x.md](doc/zh/cocos2d-x.md) |
| <img src="https://cdn.simpleicons.org/apachecordova/35434F" width="18" /> **Cordova** | [doc/zh/cordova.md](doc/zh/cordova.md) |

以上工程中的 C / C++ 源码随工程一起编译。

## 📥 安装

按系统执行对应命令，根据提示操作即可。

**Linux / macOS / WSL**

```shell
curl -fsSL https://www.kxbuild.net/install.sh | bash
```

**Windows（PowerShell）**

```powershell
irm https://www.kxbuild.net/install.ps1 | iex
```

- 默认安装到 `~/kxbuild`（Windows 为 `%USERPROFILE%\kxbuild`），安装时会先询问，可输入其他绝对路径。安装脚本会设置 `KXBUILD_HOME` 并把其中的 `bin` 加入 `PATH`。
- Windows 上推荐在 WSL（Ubuntu）里安装。直接装在 Windows 上需要额外安装 Visual Studio C++ 生成工具（占用 10–20 GB），且 Windows 默认 260 字符的路径长度限制可能导致层级很深的工程编译失败。

装完后**新开一个终端**检查环境，全部检查通过即可开始使用。

```shell
kxbuild doctor
```

以后升级执行 `kxbuild update`，它会安装最新版 kxbuild 并自动运行 doctor。

## ⌨️ 快速开始

**1. 创建工程**（已有工程可跳过）

```shell
kxbuild project create -t app_swiftui -n MyApp -b com.example.myapp -o D:\projects
```

`kxbuild project templates` 列出全部模板：SwiftUI、UIKit、Objective-C、CocoaPods、Flutter、React Native、Expo、Framework 和静态库。

**2. 配置签名**

把 .p12 证书和 .mobileprovision 描述文件放到工程根目录的 `keychain` 文件夹，并写好 `password.properties`。详见[签名](#-签名)。

**3. 构建并安装到 iPhone**

用 USB 连接 iPhone，点「信任此电脑」，并打开开发者模式（设置 → 隐私与安全性 → 开发者模式），然后执行：

```shell
kxbuild build D:\projects\MyApp\MyApp.xcodeproj --install
```

**4. 构建 Release .ipa**

```shell
kxbuild build D:\projects\MyApp\MyApp.xcodeproj -c Release
```

安装包生成在工程目录下的 `build\MyApp.ipa`。用 Transporter 或其他 App Store 上传工具上传到 App Store Connect，TestFlight 和审核流程照常进行。

## 🛠️ kxbuild 命令

| 命令 | 作用 |
|---|---|
| `kxbuild doctor [工程]` | 检查并修复构建环境。带工程路径时还会准备该工程（Flutter、React Native、Expo、Kotlin Multiplatform、cocos2d-x 首次构建前必须执行） |
| `kxbuild build <工程>` | 编译、链接、签名并打包 .xcodeproj、.xcworkspace 或含 Package.swift 的目录，可选安装到设备 |
| `kxbuild test <工程>` | 构建 Scheme 的测试并在已连接的 iOS 17+ 设备上运行（XCTest 与 Swift Testing） |
| `kxbuild resolve <工程>` | 解析 Swift Package 依赖 |
| `kxbuild project …` | 查看、创建、编辑、转换 Xcode 工程：`info`、`templates`、`create`、`settings`、`add-setting`、`add-package`、`add-target`、`add-folder`、`add-resource`、`localization`、`spm2app`、`to-spm` |
| `kxbuild config get/set/unset` | 读取或修改本机设置 |
| `kxbuild update` | 升级 kxbuild，然后运行 doctor |
| `kxbuild version` | 显示版本信息 |

每个命令都支持 `--help`，可查看完整参数。

### `kxbuild build` 参数

```text
kxbuild build <project.xcodeproj|project.xcworkspace|package dir> [flags]
```

| 参数 | 说明 |
|---|---|
| `-s, --scheme NAME` / `-t, --target NAME` | 要构建的 Scheme 或 Target，都不指定时构建默认 Scheme |
| `-c, --config NAME` | 构建配置，默认取 Scheme 的配置，否则为 Debug |
| `--sdk NAME`、`--arch ARCH`、`--toolchain NAME` | SDK（`iphoneos`、`iphonesimulator`、`macosx`）、架构和工具链 |
| `-D, --setting KEY=VALUE` | 覆盖编译设置，可重复，优先级与 xcodebuild 的 `KEY=VALUE` 相同 |
| `--xcconfig PATH` | 应用一个 .xcconfig 文件，同 xcodebuild `-xcconfig` |
| `-o, --ipa PATH` / `--no-ipa` | 指定 .ipa 输出路径，或不打包 |
| `-i, --install` | 构建成功后把签好名的 App 安装到已连接的 iPhone |
| `--derivedDataDir DIR`、`--clean` | 构建输出根目录（默认 `~/DerivedData`），以及构建前清空它 |
| `--ignoreDependence` | 只构建指定的 Target，跳过其依赖 |
| `-j, --jobs N`、`--explain`、`--timing`、`-v, --verbose`、`--no-cache` | 并行度与诊断 |
| `--emit-commands-only` | 只生成供编辑器代码补全用的 `compile_commands.json`，不编译 |
| `--skip-package-updates`、`--only-use-versions-from-resolved-file`、`--disable-package-repository-cache`、`--package-cache-path DIR` | Swift Package 解析选项，与 xcodebuild 同义选项一致 |

### 构建产物

| 产物 | 位置 |
|---|---|
| .app 与中间文件 | `~/DerivedData/<Name>-<hash>/`（可用 `--derivedDataDir` 修改） |
| .ipa | Release 为 `<工程目录>/build/<App>.ipa`，其他配置为 `<App>-<Config>.ipa` |
| `<App>.AppStoreInfo.plist` | 位于 Release .ipa 旁边，供命令行版 Transporter 的 `-assetDescription` 使用 |

### 常用命令

| 目标 | 命令 |
|---|---|
| Debug 构建并装到 iPhone | `kxbuild build .\MyApp.xcodeproj --install` |
| 生成上架用的 Release .ipa | `kxbuild build .\MyApp.xcodeproj -c Release` |
| 命令行指定发布证书 | `kxbuild build .\MyApp.xcodeproj -c Release -D CODE_SIGN_IDENTITY="Apple Distribution"` |
| 本次构建指定版本号和 Build 号 | `kxbuild build .\MyApp.xcodeproj -D MARKETING_VERSION=1.2.0 -D CURRENT_PROJECT_VERSION=45` |
| 只检查能否编译，不签名 | `kxbuild build .\MyApp.xcodeproj -D CODE_SIGNING_ALLOWED=NO` |
| 用模拟器 SDK 检查编译 | `kxbuild build .\MyApp.xcodeproj --sdk iphonesimulator -D CODE_SIGNING_ALLOWED=NO` |
| 只构建某个库 Target | `kxbuild build .\MyApp.xcodeproj -t MyKit --ignoreDependence` |
| 重置 Swift Package 缓存 | `kxbuild resolve .\MyApp.xcworkspace --force` |
| 列出 Scheme、Target 和 Bundle ID | `kxbuild project info .\MyApp.xcodeproj` |

## 🔁 脚本与 CI

所有命令成功时退出码为 0，失败时为非 0。典型的 CI 发布构建命令：

```shell
kxbuild build MyApp.xcworkspace -s MyApp -c Release --clean --derivedDataDir ./out --skip-package-updates --ipa ./out/MyApp.ipa --json
```

加 `--json` 后，stdout 每行是一个 JSON 事件（`log`、`progress`、`diagnostic`，最后一个是 `result`）：

```json
{"type":"progress","phase":"CompileSwiftSources","current":12,"total":42,"target":"MyApp"}
{"type":"result","command":"build","schemaVersion":1,"data":{"success":true,"duration":"1m12s","output":"…","products":["…"],"ipa":["out/MyApp.ipa"],"schemaVersion":1}}
```

排查问题时不要加 `--json`，错误详情只在普通输出里显示。在 CI 中可设置环境变量 `KXBUILD_NO_UPDATE_CHECK` 关闭升级提示。

## 🔧 兼容 xcodebuild

安装目录的 `bin` 下有与 macOS 同名的 `xcodebuild`、`xcrun`、`codesign`。React Native、Expo、Flutter、Cordova 的构建脚本以及你自己的脚本调用它们时，运行的就是 kxbuild 提供的版本，脚本无需修改。macOS 上系统自带的命令在 `PATH` 中优先，请直接使用 `kxbuild build`。

```shell
xcodebuild -workspace ios/MyApp.xcworkspace -scheme MyApp -configuration Debug -sdk iphoneos
xcodebuild -list -json
xcodebuild -showBuildSettings -project MyApp.xcodeproj
xcrun --sdk iphoneos --show-sdk-path
```

| 支持 | |
|---|---|
| 动作 | `build`（默认）、`clean`、`install`、`installhdrs`、`installapi`、`installsrc`、`archive`（最小、未签名的 .xcarchive）、`build-for-testing`、`test`、`test-without-building`（测试在已连接的 iOS 17+ 设备上运行） |
| 选择 | `-project`、`-workspace`、`-scheme`、`-target`、`-alltargets`、`-configuration`、`-sdk`、`-arch`、`-toolchain`、`-destination` |
| 设置 | `-xcconfig PATH`、`KEY=VALUE` |
| 输出 | `-derivedDataPath`、`-archivePath`、`-clonedSourcePackagesDirPath`、`-resolvePackageDependencies` |
| 查询 | `-list`、`-showBuildSettings`、`-showsdks`、`-showdestinations`、`-version`，均可加 `-json` |
| 控制 | `-jobs`、`-quiet`、`-verbose`、`-hideShellScriptEnvironment` |

不支持：`analyze`、`docbuild`、`-exportArchive`、`-create-xcframework`、本地化导入导出、平台组件下载。其他 xcodebuild 选项会被接受并忽略。退出码与 xcodebuild 一致：64 用法错误，65 构建失败或不支持的动作，66 输入文件不存在。要生成用于分发的 .ipa，请用 `kxbuild build -c Release`。

`xcrun` 查找并运行 SDK 和工具链中的工具（`--show-sdk-path`、`--show-sdk-version`、`-f TOOL`、`xcrun clang …`）。`codesign` 的签名与校验参数、输出和退出码都与 macOS 一致。

## 🔏 签名

需要从 Apple 开发者账号获取 .p12 证书（含私钥）和 .mobileprovision 描述文件：开发测试用 Apple Development 证书 + 包含 iPhone UDID 的开发描述文件；上架用 Apple Distribution 证书 + App Store 描述文件。

把它们放进 `keychain` 文件夹。工程根目录下的只对该工程生效，`KXBUILD_HOME` 下的对所有工程共享，两处的证书会合并；macOS 上还会使用系统钥匙串。

```text
keychain/
├─ development.p12
├─ distribution.p12
├─ MyApp_Dev.mobileprovision
├─ MyApp_AppStore.mobileprovision
└─ password.properties
```

`password.properties` 每个证书一行，格式为 `文件名=密码`（文件名与磁盘上完全一致，密码不加引号）：

```properties
development.p12=123456
distribution.p12=123456
```

与 Xcode 一样，由工程的签名设置决定使用哪个证书和描述文件：`CODE_SIGN_IDENTITY`、`DEVELOPMENT_TEAM`，以及按 Bundle ID 和团队自动匹配或按 `PROVISIONING_PROFILE_SPECIFIER` 指定。单次构建可用 `-D` 覆盖。没有证书时工程照样能构建，但产物未签名，无法安装到 iPhone。

## 📱 安装到设备

kxbuild 会一并安装用于通过 USB 与 iPhone 通信的 `kxdevice`。

```shell
kxdevice setup                    # 首次：安装设备驱动 / usbmuxd
kxdevice device list              # 第一列是 UDID
kxbuild build .\MyApp.xcodeproj --install
kxdevice install .\build\MyApp.ipa
```

| 系统 | `kxdevice setup` 做什么 |
|---|---|
| Windows | 启动 Apple Mobile Device Service，未安装时先装 Apple Devices 或 iTunes（需一次管理员授权） |
| WSL | 通过中继使用 Windows 侧的服务。iPhone 插在 Windows 上即可，不要用 usbipd 直通 |
| Linux | 安装并启动 usbmuxd 及 udev 规则（需要 sudo） |
| macOS | 无需安装 |

连接了多台设备时，`--install` 使用第一台；用 `kxdevice` 的 `--udid` 参数指定设备。

## ❓ 常见问题

**需要修改 Xcode 工程吗？**

不需要。kxbuild 原样读取工程文件，工程照样可以用 Xcode 打开。

**构建产物能上架 App Store 吗？**

可以。Release 构建生成的 .ipa 结构符合 App Store Connect 要求，直接上传 `build/<App>.ipa` 即可，TestFlight 和审核照常进行。

**安装后提示 `kxbuild: command not found`**

新开一个终端让 `PATH` 生效。仍然不行时，检查安装目录下的 `bin` 是否在 `PATH` 中。

**构建时找不到 Pods 里的头文件或模块**

你构建的是 .xcodeproj。先执行 `pod install`，然后构建生成的 .xcworkspace。

**Debug 构建链接失败：`undefined symbol: …vpfi`**

开源 Swift 编译器逐文件编译时的已知问题。设置环境变量 `KXBUILD_SWIFT_WHOLE_MODULE=all`（或单次构建加 `-D KXBUILD_SWIFT_WHOLE_MODULE=all`），所有 Swift Target 改为整模块编译。

**`No signing certificate matching '…' found`**

keychain 文件夹里没有工程要求的类型或团队的证书。补上对应的 .p12，或用 `-D CODE_SIGN_IDENTITY=… -D DEVELOPMENT_TEAM=…` 指定已有的证书。

**其他问题**

执行 `kxbuild doctor -v`（与工程有关时带上工程路径）。它能修复大多数环境问题，其输出也是反馈问题时最有用的信息。

## 📣 反馈

<div align="center">

🐛 发现问题或有建议？[提交 Issue](../../issues/new)<br/>
请附上 `kxbuild version` 和 `kxbuild doctor -v` 的输出，以及出错命令的完整日志。


</div>
