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

### 5. Hot reload and breakpoints

To see Dart changes as you make them, work in VS Code:

1. Install the KXBuild extension and the official Dart extension, and open the project folder.
2. Select the iPhone in the KXBuild view and press F5. The app is built in the Debug configuration, installed and launched, and the Dart debugger attaches once it is running. The first debug session on a given iOS version downloads its symbols once; later sessions start right away.
3. Edit Dart code and save: hot reload pushes the change to the phone immediately and keeps the screen's state. Use Hot Restart on the debug toolbar when `main()` needs to run again.
4. Set Dart breakpoints in `.dart` files; breakpoints in the native code under `ios/Runner` work at the same time.

After changing dependencies in `pubspec.yaml`, native code under `ios/` or Info.plist, stop debugging and press F5 again. There is no hot reload from the command line: a Debug build exits as soon as the debugger goes away, so on the command line use the Release build above to try the app, and rebuild after Dart changes.

### 6. Release

Build with the Release configuration to get `build\Runner.ipa` in the `ios` folder, ready to upload to App Store Connect.

```shell
kxbuild build .\ios\Runner.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| A Run Script phase fails, for example because `FLUTTER_ROOT` isn't set | Run `kxbuild doctor <project dir>` with the project path. It sets up what the Flutter scripts need |
| The build can't find plugin headers or modules | Run `pod install` in the `ios` folder, then build `Runner.xcworkspace`, not `Runner.xcodeproj` |
