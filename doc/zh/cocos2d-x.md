# 用 kxbuild 构建 cocos2d-x iOS 游戏

[English](../en/cocos2d-x.md) · **简体中文**

kxbuild 在 Windows、Linux 或 macOS 上构建 cocos2d-x 游戏的 iOS 版本，完成签名并安装到 iPhone。支持 3.x 和 4.x。引擎源码作为子工程随游戏一起编译，引擎和工程设置都无需修改。

## 步骤

### 1. 安装 cocos2d-x 并创建工程

已有工程可跳过。否则按 cocos2d-x 官网说明下载引擎及其依赖，在引擎目录执行 `setup.py` 配置好 `cocos` 命令，然后创建工程。

```shell
cocos new MyGame -l cpp -p com.example.mygame -d .
```

### 2. 对工程运行 doctor

首次构建某个工程前执行。doctor 会检查 cocos2d-x 环境并准备好 iOS 工程。4.x 工程还会在这一步于 `build-xcode` 目录生成 Xcode 工程。

```shell
kxbuild doctor D:\work\MyGame
```

### 3. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后在工程根目录按引擎版本执行对应命令。

**3.x 工程**

```shell
kxbuild build .\proj.ios_mac\MyGame.xcodeproj -t MyGame-mobile --install
```

**4.x 工程**

```shell
kxbuild build .\build-xcode\MyGame.xcodeproj -t MyGame --install
```

### 4. 发布

用 Release 配置构建，得到 .ipa。

**3.x 工程**

```shell
kxbuild build .\proj.ios_mac\MyGame.xcodeproj -c Release -t MyGame-mobile
```

**4.x 工程**

```shell
kxbuild build .\build-xcode\MyGame.xcodeproj -c Release -t MyGame
```
