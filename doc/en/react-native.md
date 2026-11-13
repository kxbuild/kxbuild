# Build the iOS app of a React Native project with kxbuild

**English** · [简体中文](../zh/react-native.md)

kxbuild builds the iOS side of a React Native project on Windows, Linux or macOS. Run Metro for the JS as usual. Build scripts in your project that call `xcodebuild` or `xcrun` work without changes, because kxbuild provides commands of the same name. See [xcodebuild compatibility](../../README.md#-xcodebuild-compatibility).

## Steps

### 1. Set up Node.js

Install Node.js as described on the React Native website. Open a new terminal and check that this prints a version.

```shell
node --version
```

### 2. Create a project

Skip this step for an existing project. Otherwise create one from the template, which initializes it with the React Native Community CLI. Keep the parent path short, because the dependency folders nest deeply.

```shell
kxbuild project create -t react_native_app -n rnhello -b com.example.rnhello -o D:\work
```

### 3. Run doctor on the project

Do this the first time you build a project. Doctor checks your React Native setup and prepares the iOS project.

```shell
kxbuild doctor D:\work\rnhello
```

After doctor, install the native dependencies in the `ios` folder. See [CocoaPods](cocoapods.md).

```shell
cd ios
pod install
```

### 4. Run on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB and run this in the project folder:

```shell
kxbuild run .\ios\rnhello.xcworkspace
```

kxbuild builds, installs and launches the app, and starts Metro in the same terminal. The app loads its JS from Metro over the USB cable, so the iPhone and the computer don't need to share a Wi-Fi network. If Metro is already running in another terminal, kxbuild uses it; press Ctrl-C to stop.

With the KXBuild extension in VS Code, selecting the device and pressing F5 does the same: the extension starts Metro and tells you once the app has connected.

### 5. See your changes right away

Keep the Metro terminal (or the VS Code window) open while the app runs. What to do depends on what you changed:

| You changed | What to do |
|---|---|
| JS / TypeScript, styles, images | Just save. Fast Refresh updates the phone within seconds and keeps the screen's state. If nothing changes, press `r` in the Metro terminal, or shake the phone and tap Reload |
| Swift / Objective-C under `ios/`, or Info.plist | Run `kxbuild run` again (press F5 again in VS Code) |
| Added or removed an npm package with native code | Run `pod install` in `ios`, then `kxbuild run` again |

Debugging:

- JS breakpoints and logs: press `j` in the Metro terminal to open React Native DevTools; set breakpoints in the Sources panel and read `console.log` output in Console.
- Native breakpoints: run with F5 in VS Code and set breakpoints in the Swift / Objective-C code. The two debuggers work independently.

### 6. Release

Build with the Release configuration. The JS is bundled into the app, and the .ipa is written to the `ios` folder.

```shell
kxbuild build .\ios\rnhello.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| The app shows a red screen on launch | The app can't reach Metro. Start the app with `kxbuild run` and keep its terminal open, with the iPhone connected over USB. Once Metro is ready, tap Reload JS in the app |
| Builds and JS loading are slow in WSL | Keep the project under your Linux home (`~/`), not on a Windows drive under `/mnt/` |
