# 用 kxbuild 构建 Kotlin Multiplatform 工程的 iOS 应用

[English](../en/kotlin-multiplatform.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上构建 Kotlin Multiplatform 工程的 iOS 应用。`iosApp` 工程和 Gradle 构建脚本都无需修改，Compose Multiplatform 同样支持。

## 步骤

### 1. 准备环境

按 Kotlin Multiplatform 文档安装 JDK 和 Android 工具（例如 Android Studio 及其 Kotlin Multiplatform 插件）。

### 2. 创建工程

已有工程可跳过。否则用 Android Studio 或 JetBrains 官网的 Kotlin Multiplatform 向导创建，并勾选 iOS 目标。

### 3. 对 iOS 工程运行 doctor

首次构建某个工程前执行。doctor 会检查 Kotlin Multiplatform 环境并准备好 iOS 工程。第一次运行要下载几百 MB，需要一些时间。升级 Kotlin 后请再执行一次。

```shell
kxbuild doctor D:\work\mykmpapp\iosApp\iosApp.xcodeproj
```

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后在工程根目录执行。构建共享 Kotlin framework 的 Gradle 任务会在构建过程中自动运行。

```shell
kxbuild build .\iosApp\iosApp.xcodeproj --install
```

### 5. 发布

用 Release 配置构建，.ipa 生成在 `iosApp` 目录下。

```shell
kxbuild build .\iosApp\iosApp.xcodeproj -c Release
```

## 常见问题

| 问题 | 解决办法 |
|---|---|
| Run Script 阶段失败，例如没有设置 `JAVA_HOME` 或 Kotlin/Native 未就绪 | 带 iOS 工程路径执行 `kxbuild doctor`，它会配置好 Gradle 构建需要的环境 |
