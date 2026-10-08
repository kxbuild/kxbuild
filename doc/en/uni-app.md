# Build the iOS app of a uni-app project with kxbuild

**English** · [简体中文](../zh/uni-app.md)

Package the iOS app of an HBuilderX uni-app project locally on Windows, with no cloud packaging and no queue. First export the uni-app project as an Xcode project with `uniapp2xcode`, which is installed with kxbuild, then build it like any other iOS project.

## Steps

### 1. Prepare the uni-app project and the offline SDK

Create and develop your uni-app project in HBuilderX as usual. Then download the SDK matching your HBuilderX version from DCloud's [iOS offline SDK download page](https://nativesupport.dcloud.net.cn/AppDocs/download/ios.html) and unzip it.

For Vue 2 projects, HBuilderX can't be installed in a folder whose path contains parentheses (such as `Program Files (x86)`), or page compilation fails.

### 2. Export an Xcode project

`uniapp2xcode` compiles the pages with HBuilderX, generates an Xcode project from them and the SDK, and installs the modules checked in manifest.json with [CocoaPods](cocoapods.md).

```shell
uniapp2xcode --project D:\work\myuniapp --sdk D:\sdk\HBuilder-iOS-SDK --output D:\work\myuniapp-ios --bundle-id com.example.myapp --dcloud-appkey YOUR_APPKEY --clean
```

| Option | Description |
|---|---|
| `-p, --project DIR` | uni-app project directory, containing manifest.json. Required |
| `--sdk DIR` | Root of the DCloud iOS offline SDK. Required |
| `-o, --output DIR` | Output directory for the generated Xcode project. Required |
| `--dcloud-appkey KEY` | DCloud app key for your bundle ID. Required, or the app reports an AppKey error at launch |
| `--bundle-id ID` | Bundle ID. Required if `nativeResources/ios/Info.plist` doesn't set one |
| `--clean` | Empties the output directory before exporting |
| `--display-name`, `--version-name`, `--version-code`, `--team-id`, `--hbx DIR` | App name, version, team ID, HBuilderX install directory |
| `--skip-build` / `--force-build` | Reuse the existing page build output, or always recompile |

The app name, version, icons and permission descriptions come from manifest.json. Put any extra Info.plist keys in `nativeResources/ios/Info.plist` in the uni-app project. After you change pages in HBuilderX, run the same command again.

### 3. Run doctor

```shell
kxbuild doctor D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace
```

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then:

```shell
kxbuild build D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace -s HBuilder --install
```

### 5. Release

Build with the Release configuration to get an .ipa named after your app under `HBuilder-Hello\build` in the output folder.

```shell
kxbuild build D:\work\myuniapp-ios\HBuilder-Hello\HBuilder-Hello.xcworkspace -s HBuilder -c Release
```

## FAQ

| Problem | Fix |
|---|---|
| Export says you're not signed in or the AppID doesn't exist, or the app shows an AppKey error as soon as it opens | The AppID, AppKey and bundle ID are one bound set. Sign in to your DCloud account in HBuilderX and get the AppID from the basic settings in manifest.json. Then at dev.dcloud.net.cn, apply for the offline packaging AppKey on this app's iOS platform with your bundle ID. The bundle ID you export with must match the one used for the AppKey and the App ID in your Apple Developer account |
| Page compilation fails because the HBuilderX install folder contains "(" | A limit for Vue 2 projects. Move HBuilderX to a path like `D:\HBuilderX`, or switch the project to Vue 3 in manifest.json |
| Version mismatch or blank white screen after launch | The offline SDK and HBuilderX versions differ. Download the SDK matching your HBuilderX and export again |
| Export reports skipped modules at the end | The current SDK doesn't include the module, or its libraries can't be linked. The other modules are packaged as usual. You can also edit the `uniapp_subspecs` list in `HBuilder-Hello\Podfile` and run `pod install` there |
