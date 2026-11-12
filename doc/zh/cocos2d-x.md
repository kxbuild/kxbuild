# 用 kxbuild 构建 Cocos Creator 和 cocos2d-x iOS 游戏

[English](../en/cocos2d-x.md) · **简体中文**

kxbuild 构建 Cocos Creator 3.8、Cocos 4 游戏的 iOS 版本，完成签名并安装到 iPhone。引擎随游戏一起编译，引擎和工程设置都无需修改。cocos2d-x 3.x、4.x 的老项目同样支持，见 [cocos2d-x 3.x 和 4.x](#cocos2d-x-3x-和-4x)。

## Cocos Creator

### 1. 用 cocos-cli 构建 iOS 平台

Cocos Creator 编辑器在 Windows 上没有 iOS 构建选项，iOS 平台用 cocos-cli 构建。按 [cocos-cli 仓库](https://github.com/cocos/cocos-cli)的说明安装（需要 Node.js 22），Creator 3.8 的项目也用它构建。

在工程根目录新建 `ios-build.json`，把包名换成你自己的 Bundle ID：

```json
{
  "platform": "ios",
  "debug": true,
  "packages": {
    "ios": {
      "packageName": "com.example.mygame",
      "skipUpdateXcodeProject": true
    }
  }
}
```

然后在工程根目录构建：

```shell
cocos build --project . --platform ios --build-config ios-build.json
```

第一次构建的最后一步会报 Xcode 生成器找不到，这时游戏资源已经构建好，直接做第 2 步。游戏的起始场景不是工程里的第一个场景时，在 `ios-build.json` 里加上 `"startScene": "场景的 uuid"`，uuid 在场景旁边的 `.scene.meta` 文件里。

### 2. 对工程运行 doctor

第一次构建之后执行。doctor 在 `build\ios\proj` 目录生成 Xcode 工程，最后一行给出构建和安装的命令。以后改了 `native` 目录里的原生代码，或者改了 iOS 构建选项，再运行一次。

```shell
kxbuild doctor D:\work\MyGame
```

### 3. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，执行 doctor 最后给出的命令。工程名只含字母、数字、`-` 和 `_` 时，目标名是 `工程名-mobile`，否则是 `CocosGame`。

```shell
kxbuild build .\build\ios\proj\MyGame.xcodeproj -t MyGame-mobile --install
```

在 Cocos Creator 编辑器里改了场景、脚本或资源之后，重新执行第 1 步的 `cocos build` 命令，再执行这条命令即可，不用再运行 doctor。

### 4. 发布

把 `ios-build.json` 里的 `"debug": true` 改成 `"debug": false`，重新执行第 1 步的 `cocos build` 命令，再用 Release 配置构建，得到 .ipa。

```shell
kxbuild build .\build\ios\proj\MyGame.xcodeproj -c Release -t MyGame-mobile
```

用 cocos-cli 构建 Creator 3.8 的工程时，只会调整 `package.json` 的格式并新增 `settings/cocos.config.json`，提交代码前看一下这两处改动。

## cocos2d-x 3.x 和 4.x

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
