# Build Cordova iOS apps with kxbuild

**English** · [简体中文](../zh/cordova.md)

kxbuild packages a Cordova project for iOS on Windows, Linux or macOS. The `platforms/ios` project that `cordova platform add ios` creates builds like any Xcode project, and `cordova` commands and plugins work as usual.

## Steps

### 1. Create a Cordova project and add the iOS platform

For an existing project, add the iOS platform in the project folder. Otherwise install Cordova with npm, create the project, then add the iOS platform from inside the project folder. The Xcode project is generated under `platforms/ios`.

```shell
npm install -g cordova
cordova create MyApp com.example.myapp MyApp
cd MyApp
cordova platform add ios
```

### 2. Run doctor

Doctor checks the build environment and prepares the iOS project.

```shell
kxbuild doctor .\platforms\ios\App.xcworkspace
```

By default the project manages dependencies with Swift packages and doesn't need CocoaPods. If a plugin you add has CocoaPods dependencies, a Podfile appears in `platforms/ios`. Cordova doesn't install those pods on Windows, so run `pod install` once in `platforms/ios`, and again whenever you add or remove such a plugin. See [CocoaPods](cocoapods.md).

```shell
cd platforms\ios
pod install
```

### 3. Sync your web code

Keep writing your web code under `www/`, and run this after changes to copy it into the iOS project.

```shell
cordova prepare ios
```

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then:

```shell
kxbuild build .\platforms\ios\App.xcworkspace --install
```

### 5. Release

Build with the Release configuration to get an .ipa under `platforms/ios`.

```shell
kxbuild build .\platforms\ios\App.xcworkspace -c Release
```
