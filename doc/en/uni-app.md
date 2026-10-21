# Build the iOS app of a uni-app project with kxbuild

**English** · [简体中文](../zh/uni-app.md)

Package the iOS app of an HBuilderX uni-app project locally on Windows, with no cloud packaging and no queue. First export the uni-app project as an Xcode project with `uniapp2xcode`, which is installed with kxbuild, then build it like any other iOS project.

## Steps

### 1. Prepare the uni-app project and the offline SDK

Create and develop your uni-app project in HBuilderX as usual. Then download the SDK zip matching your HBuilderX version from DCloud's [iOS offline SDK download page](https://nativesupport.dcloud.net.cn/AppDocs/download/ios.html) and import it once, without unzipping:

```shell
uniapp2xcode sdk import D:\Downloads\iOSSDK.zip
```

Save the bundle ID and the offline packaging AppKey in the project, so later exports don't ask for them:

```shell
uniapp2xcode config --project D:\work\myuniapp --bundle-id com.example.myapp --dcloud-appkey YOUR_APPKEY
```

For Vue 2 projects, HBuilderX can't be installed in a folder whose path contains parentheses (such as `Program Files (x86)`), or page compilation fails.

### 2. Export an Xcode project

`uniapp2xcode` compiles the pages with HBuilderX, generates an Xcode project from them and the SDK, and installs the modules checked in manifest.json with [CocoaPods](cocoapods.md).

```shell
uniapp2xcode --project D:\work\myuniapp --sdk auto --output D:\work\myuniapp-ios --update
```

| Option | Description |
|---|---|
| `-p, --project DIR` | uni-app project directory, containing manifest.json. Required |
| `--sdk DIR` | Root of the DCloud iOS offline SDK. Required. `auto` uses the imported SDK that matches your HBuilderX version |
| `-o, --output DIR` | Output directory for the generated Xcode project. Required |
| `--dcloud-appkey KEY` | DCloud app key for your bundle ID. Required unless saved in the project with `config`, or the app reports an AppKey error at launch |
| `--bundle-id ID` | Bundle ID. Required if `nativeResources/ios/Info.plist` doesn't set one |
| `--update` | Updates an earlier export in the output directory: if only pages changed, only the pages are replaced, in seconds; otherwise it exports again |
| `--clean` | Empties the output directory before exporting |
| `--display-name`, `--version-name`, `--version-code`, `--team-id`, `--hbx DIR` | App name, version, team ID, HBuilderX install directory |
| `--skip-build` / `--force-build` | Reuse the existing page build output, or always recompile |

The app name, version, icons and permission descriptions come from manifest.json. Put any extra Info.plist keys in `nativeResources/ios/Info.plist` in the uni-app project. After you change pages in HBuilderX, run the same command again. `uniapp2xcode config --project DIR` shows the project's current bundle ID and AppKey.

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
| After changing pages and installing again, the iPhone still shows the old pages | When installed over an existing copy, the app keeps using the pages from before. Delete the app on the iPhone (or run `kxdevice apps uninstall <bundle ID>`) and install again. The app's local data is deleted with it |
| Export says no SDK matches your HBuilderX version | Download the iOS offline SDK zip for that version and run `uniapp2xcode sdk import <zip>` |
| Export reports skipped modules at the end | The current SDK doesn't include the module, or its libraries can't be linked. The other modules are packaged as usual. You can also edit the `uniapp_subspecs` list in `HBuilder-Hello\Podfile` and run `pod install` there |
