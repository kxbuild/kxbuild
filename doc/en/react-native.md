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

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing) and connect the iPhone over USB. A Debug build loads its JS from Metro, so start Metro in a second terminal in the project folder:

```shell
npx react-native start
```

Then build and install:

```shell
kxbuild build .\ios\rnhello.xcworkspace --install
```

The iPhone and the computer must be on the same network so the app can reach Metro.

### 5. Release

Build with the Release configuration. The JS is bundled into the app, and the .ipa is written to the `ios` folder.

```shell
kxbuild build .\ios\rnhello.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| The app shows a red screen on launch | The iPhone can't reach Metro. Put the iPhone and the computer on the same network, and allow the Metro port through the firewall |
| A Run Script phase fails | Run `kxbuild doctor <project dir>` with the project path. It installs what the React Native scripts need |
| Path errors on Windows | Keep the project in a short path such as `D:\work`. Doctor turns on Windows long path support (one admin approval) |
