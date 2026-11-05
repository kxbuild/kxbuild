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

### 5. 改网页代码不重新构建

默认每次改 `www/` 都要 `cordova prepare ios` 再重新构建安装。开发阶段可以改成让 App 通过数据线直接从电脑上读 `www/`，改完几秒就能在手机上看到，不用重新构建。配置一次即可：

1. 在工程目录建一个 `ops.json`，告诉 kxbuild 开发服务器的端口和启动命令：

   ```json
   [
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_PORTS","value":"8000"},
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_COMMAND","value":"python -m http.server 8000 --bind 127.0.0.1 --directory platforms/ios/www"},
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_DIR","value":"$(SRCROOT)/../.."}
   ]
   ```

   然后写进 iOS 工程（需要已安装 Python 3）：

   ```shell
   kxbuild project settings apply .\platforms\ios\App.xcworkspace --ops ops.json
   ```

2. 在 `config.xml` 里把首页改成从电脑加载，并允许访问：

   ```xml
   <content src="http://127.0.0.1:8000/index.html" />
   <allow-navigation href="http://127.0.0.1:*/*" />
   ```

3. 同步一次并运行：

   ```shell
   cordova prepare ios
   kxbuild run .\platforms\ios\App.xcworkspace
   ```

   kxbuild 构建、安装并启动 App，同时在这个终端启动开发服务器和数据线连接。在 VS Code 里按 F5 效果相同。

之后的日常开发：改 `www/` 下的代码，执行 `cordova prepare ios`，再重新打开 App（或执行 `kxbuild run .\platforms\ios\App.xcworkspace --no-build --no-install --kill-existing`，几秒），新页面就显示出来了。只有改了原生插件代码或 `config.xml` 时才需要重新构建。原生插件代码可以在 VS Code 里按 F5 运行并打断点。

发布前把 `content src` 改回 `index.html`，Release 包不受上面设置的影响。

### 6. 发布

用 Release 配置构建，.ipa 生成在 `platforms/ios` 下。

```shell
kxbuild build .\platforms\ios\App.xcworkspace -c Release
```
