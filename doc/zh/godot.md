# 用 kxbuild 构建 Godot 导出的 iOS 工程

[English](../en/godot.md) · **简体中文**

Godot 导出的 iOS 工程可以用 kxbuild 在 Windows、Linux 或 macOS 上构建并安装到 iPhone。照常从 Godot 导出，像普通 Xcode 工程一样构建导出目录即可，Godot 工程设置保持不变。

## 步骤

### 1. 从 Godot 导出 Xcode 工程

照常在 Godot 中开发。在 **编辑器 > 管理导出模板** 中下载导出模板，在 **项目 > 导出** 中添加 iOS 预设，填写 App Store Team ID 和 Bundle Identifier，然后导出到 Godot 工程之外的一个空目录，文件名如 `MyGame.ipa`。在 Windows 上，Godot 会生成 Xcode 工程 `MyGame.xcodeproj` 而不是 .ipa。

### 2. 运行 doctor

doctor 检查构建环境，并补齐缺少的部分。

```shell
kxbuild doctor .\MyGame.xcodeproj
```

### 3. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后执行：

```shell
kxbuild build .\MyGame.xcodeproj --install
```

### 4. 发布

用 Release 配置构建，.ipa 生成在导出目录下。

```shell
kxbuild build .\MyGame.xcodeproj -c Release
```

修改 GDScript 或场景后，重新导出到同一目录再构建即可。
