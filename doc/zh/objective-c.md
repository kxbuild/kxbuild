# 用 kxbuild 构建 Objective-C iOS 应用

[English](../en/objective-c.md) · **简体中文**

kxbuild 在 Windows、Linux、macOS 上构建 Objective-C iOS 工程，完成签名并安装到 iPhone。现有的 .xcodeproj 和 .xcworkspace 原样构建：编译设置、Target 依赖和编译参数都按 Xcode 的规则读取。支持 Objective-C 与 Swift 混编，以及 C / C++ 源码。使用 CocoaPods 的工程见 [CocoaPods](cocoapods.md)。

## 步骤

### 1. 创建工程

已有工程可跳过。否则用 Objective-C 模板（`app_objc`）创建；Objective-C + Swift 混编工程用 `app_objc_swift`。

```shell
kxbuild project create -t app_objc -n MyApp -b com.example.myapp -o D:\projects
```

### 2. 运行 doctor

doctor 检查构建环境，并补齐缺少的部分。

```shell
kxbuild doctor .\MyApp.xcodeproj
```

### 3. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后执行：

```shell
kxbuild build .\MyApp.xcodeproj --install
```

### 4. 发布

用 Release 配置构建，在工程目录下得到 `build\MyApp.ipa`。

**.xcodeproj 工程**

```shell
kxbuild build .\MyApp.xcodeproj -c Release
```

**.xcworkspace 工作区**

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

## 提示

- 用 `kxbuild project info .\MyApp.xcodeproj` 列出 Scheme、Target 和 Bundle ID，再用 `-s` 或 `-t` 指定。
- 单次构建修改编译设置用 `-D KEY=VALUE`；要写入工程文件用 `kxbuild project add-setting`。
- 编辑器需要代码补全时，`kxbuild build .\MyApp.xcodeproj --emit-commands-only` 只生成供 clangd 使用的 `compile_commands.json`，不做编译。
