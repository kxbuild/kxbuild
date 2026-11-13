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

kxbuild builds, installs and launches the app, and starts the Expo dev server (`npx expo start`) in the same terminal. The app loads its JS from it over the USB cable, so the iPhone and the computer don't need to share a Wi-Fi network. If the dev server is already running in another terminal, kxbuild uses it; press Ctrl-C to stop.

With the KXBuild extension in VS Code, selecting the device and pressing F5 does the same: the extension starts the dev server and tells you once the app has connected.

### 5. See your changes right away

Keep the dev server's terminal (or the VS Code window) open while the app runs. What to do depends on what you changed:

| You changed | What to do |
|---|---|
| JS / TypeScript, styles, images, route files | Just save. Fast Refresh updates the phone within seconds and keeps the screen's state. If nothing changes, press `r` in the terminal, or shake the phone and tap Reload |
| `app.json`, `app.config.js`, or added an Expo module / npm package with native code | Delete the `ios` folder, run `kxbuild doctor` to regenerate it, run `pod install` in `ios`, then `kxbuild run` |
| Native code under `ios/` | Run `kxbuild run` again (press F5 again in VS Code). The next regeneration of `ios` overwrites manual edits |

Debugging:

- JS breakpoints and logs: press `j` in the terminal to open Expo's debugger; set breakpoints in the Sources panel and read `console.log` output in Console.
- Native breakpoints: run with F5 in VS Code and set breakpoints in the Swift / Objective-C code under `ios/`. The two debuggers work independently.

### 6. Release

Build with the Release configuration. The JS is bundled into the app, and the .ipa is written to the `ios` folder.

```shell
kxbuild build .\ios\expohello.xcworkspace -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| The app shows a red screen on launch | The app can't reach the dev server. Start the app with `kxbuild run` and keep its terminal open, with the iPhone connected over USB. Once the dev server is ready, tap Reload JS in the app |
| In WSL the build fails because scanning for Expo modules didn't complete, or JS loads slowly | Keep the project under your Linux home (`~/`), not on a Windows drive under `/mnt/` |
