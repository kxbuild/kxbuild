# 用 kxbuild 构建 uni-app 工程的 iOS 应用

[English](../en/uni-app.md) · **简体中文**

在 Windows 本地打包 HBuilderX uni-app 工程的 iOS 应用：不用云打包、不用排队。先用随 kxbuild 安装的 `uniapp2xcode` 把 uni-app 工程导出为 Xcode 工程，再像其他 iOS 工程一样构建。

## 步骤

### 1. 准备 uni-app 工程和离线 SDK

照常在 HBuilderX 中创建和开发 uni-app 工程。然后从 DCloud 的 [iOS 离线 SDK 下载页](https://nativesupport.dcloud.net.cn/AppDocs/download/ios.html)下载与 HBuilderX 版本对应的 SDK 并解压。

Vue 2 工程要求 HBuilderX 的安装路径不能包含括号（例如 `Program Files (x86)`），否则页面编译失败。

### 2. 导出 Xcode 工程

`uniapp2xcode` 调用 HBuilderX 编译页面，再结合 SDK 生成 Xcode 工程，并用 [CocoaPods](cocoapods.md) 安装 manifest.json 中勾选的模块。

```shell
uniapp2xcode --project D:\work\myuniapp --sdk D:\sdk\HBuilder-iOS-SDK --output D:\work\myuniapp-ios --bundle-id com.example.myapp --dcloud-appkey YOUR_APPKEY --clean
```

| 参数 | 说明 |
|---|---|
| `-p, --project DIR` | uni-app 工程目录（含 manifest.json），必填 |
| `--sdk DIR` | DCloud iOS 离线 SDK 根目录，必填 |
| `-o, --output DIR` | 生成的 Xcode 工程的输出目录，必填 |
| `--dcloud-appkey KEY` | 与 Bundle ID 对应的 DCloud AppKey。必填，否则 App 启动时报 AppKey 错误 |
| `--bundle-id ID` | Bundle ID。`nativeResources/ios/Info.plist` 中没有设置时必填 |
| `--clean` | 导出前清空输出目录 |
| `--display-name`、`--version-name`、`--version-code`、`--team-id`、`--hbx DIR` | App 名称、版本、团队 ID、HBuilderX 安装目录 |
| `--skip-build` / `--force-build` | 复用已有的页面编译结果，或强制重新编译 |

App 名称、版本、图标和权限描述来自 manifest.json；额外的 Info.plist 键写在 uni-app 工程的 `nativeResources/ios/Info.plist` 中。在 HBuilderX 里修改页面后，重新执行同一条命令即可。

### 3. 运行 doctor

```shell
kxbuild doctor D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace
```

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后执行：

```shell
kxbuild build D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace -s HBuilder --install
```

### 5. 发布

用 Release 配置构建，在输出目录的 `HBuilder-Hello\build` 下得到以 App 名称命名的 .ipa。

```shell
kxbuild build D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace -s HBuilder -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| 导出时提示未登录或 AppID 不存在，或 App 一打开就报 AppKey 错误 | AppID、AppKey 和 Bundle ID 是绑定的一组。在 HBuilderX 中登录 DCloud 账号，在 manifest.json 的「基础配置」中获取 AppID；再到 dev.dcloud.net.cn 为该应用的 iOS 平台用你的 Bundle ID 申请离线打包 AppKey。导出时的 Bundle ID 必须与申请 AppKey 时的以及 Apple 开发者账号中的 App ID 一致 |
| 页面编译失败，提示「HBuilderX 安装目录不能包括 ( 等特殊字符」 | Vue 2 工程的限制。把 HBuilderX 整个目录移到 `D:\HBuilderX` 这类路径，或在 manifest.json 中把工程切换为 Vue 3 |
| 版本不匹配，或启动后白屏 | 离线 SDK 与 HBuilderX 版本不一致。下载与 HBuilderX 对应的 SDK 后重新导出 |
| 导出结束时提示跳过了某些模块 | 当前 SDK 不包含该模块，或其库无法链接，其他模块照常打包。也可以在 `HBuilder-Hello\Podfile` 的 `uniapp_subspecs` 列表中增删模块，然后在该目录执行 `pod install` |
