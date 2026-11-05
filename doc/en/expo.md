# Build the iOS app of an Expo project with kxbuild

**English** · [简体中文](../zh/expo.md)

kxbuild builds the iOS side of an Expo project locally on Windows, Linux or macOS, with no cloud build service. If the project has no `ios/` folder, doctor generates it. Run the Expo dev server for the JS as usual.

## Steps

### 1. Set up Node.js

Install Node.js as described on the Expo website. Open a new terminal and check that this prints a version.

```shell
node --version
```

### 2. Create a project

Skip this step for an existing project. Otherwise create one from the template, which runs `create-expo-app`, `expo prebuild` and `pod install`. Keep the parent path short, because the dependency folders nest deeply.

```shell
kxbuild project create -t expo_app -n expohello -b com.example.expohello -o D:\work
```

### 3. Run doctor on the project

Do this the first time you build a project. Doctor checks your Expo setup, then generates and prepares the iOS project.

```shell
kxbuild doctor D:\work\expohello
```

After doctor, install the native dependencies in the `ios` folder. See [CocoaPods](cocoapods.md).

```shell
cd ios
pod install
```

### 4. Run on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB and run this in the project folder:

```shell
kxbuild run .\ios\expohello.xcworkspace
```

kxbuild builds, installs and launches the app, and starts the Expo dev server (`npx expo start`) in the same terminal; the app loads its JS from it over USB. Saving your code updates the app through Fast Refresh, and Expo's terminal shortcuts (`r` to reload, `j` to open the debugger) work as usual. Press Ctrl-C to stop the dev server.

If the dev server is already running in another terminal, kxbuild uses it. How the USB path works: [Dev server over USB](dev-server.md).

### 5. Release

Build with the Release configuration. The JS is bundled into the app, and the .ipa is written to the `ios` folder.

```shell
kxbuild build .\ios\expohello.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| The app shows a red screen on launch | The app can't reach the dev server. Start the app with `kxbuild run` and keep its terminal open, with the iPhone connected over USB. Once the dev server is ready, tap Reload JS in the app |
| In WSL the build fails because scanning for Expo modules didn't complete, or JS loads slowly | Keep the project under your Linux home (`~/`), not on a Windows drive under `/mnt/` |
| There's no `ios` folder | Run `kxbuild doctor <project dir>` with the project path. It generates the iOS project |
