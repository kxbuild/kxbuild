# Build the iOS app of a Flutter project with kxbuild

**English** · [简体中文](../zh/flutter.md)

kxbuild builds the iOS side of a Flutter project on Windows, Linux or macOS. Use it in place of `flutter build ios`; every other `flutter` command works as usual. The Flutter build scripts inside the Runner project call `xcodebuild` and `xcrun`, which kxbuild provides, so the project builds without changes.

## Steps

### 1. Install Flutter

Install the Flutter SDK as described on the Flutter website and add the `flutter` command to `PATH`. Open a new terminal and check that this prints a version.

```shell
flutter --version
```

### 2. Create a project

Skip this step for an existing project. Otherwise create one with `flutter create`, or from the kxbuild template, which runs `flutter create` and sets the bundle ID on the Runner target.

```shell
kxbuild project create -t flutter_app -n myflutterapp -b com.example.myflutterapp -o D:\work
```

### 3. Run doctor on the project

Do this the first time you build a project. Doctor checks your Flutter setup and prepares the iOS project.

```shell
kxbuild doctor D:\work\myflutterapp
```

If the project uses plugins that need CocoaPods, install their dependencies in the `ios` folder afterwards. See [CocoaPods](cocoapods.md). Otherwise, skip this.

```shell
cd ios
pod install
```

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing) and connect the iPhone over USB. Flutter Debug builds only run while a debugger is attached, so to try the app on the phone, build Release and install it:

```shell
kxbuild build .\ios\Runner.xcworkspace -c Release --install
```

### 5. Release

Build with the Release configuration to get `build\Runner.ipa` in the `ios` folder, ready to upload to App Store Connect.

```shell
kxbuild build .\ios\Runner.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| A Run Script phase fails, for example because `FLUTTER_ROOT` isn't set | Run `kxbuild doctor <project dir>` with the project path. It sets up what the Flutter scripts need |
| The build can't find plugin headers or modules | Run `pod install` in the `ios` folder, then build `Runner.xcworkspace`, not `Runner.xcodeproj` |
