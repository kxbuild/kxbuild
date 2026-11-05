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

### 5. Change web code without rebuilding

By default every change under `www/` takes a `cordova prepare ios` and a rebuild. During development you can have the app read `www/` straight from the computer over the USB cable instead, so a change shows up on the phone in seconds with no rebuild. Set it up once:

1. In the project folder, create `ops.json` with the dev server's port and command for kxbuild:

   ```json
   [
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_PORTS","value":"8000"},
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_COMMAND","value":"python -m http.server 8000 --bind 127.0.0.1 --directory platforms/ios/www"},
     {"op":"set","owner":"App","config":"Debug","key":"KXBUILD_DEV_SERVER_DIR","value":"$(SRCROOT)/../.."}
   ]
   ```

   Then write it into the iOS project (Python 3 must be installed):

   ```shell
   kxbuild project settings apply .\platforms\ios\App.xcworkspace --ops ops.json
   ```

2. In `config.xml`, make the start page load from the computer and allow it:

   ```xml
   <content src="http://127.0.0.1:8000/index.html" />
   <allow-navigation href="http://127.0.0.1:*/*" />
   ```

3. Sync once and run:

   ```shell
   cordova prepare ios
   kxbuild run .\platforms\ios\App.xcworkspace
   ```

   kxbuild builds, installs and launches the app, and starts the dev server and the USB connection in the same terminal. Pressing F5 in VS Code does the same.

Day to day: edit the code under `www/`, run `cordova prepare ios`, then reopen the app (or run `kxbuild run .\platforms\ios\App.xcworkspace --no-build --no-install --kill-existing`, a few seconds) and the new page is there. Only native plugin code and `config.xml` changes need a rebuild. Native plugin code can be run with F5 in VS Code and debugged with breakpoints.

Set `content src` back to `index.html` before you release; Release builds ignore the settings above.

### 6. Release

Build with the Release configuration to get an .ipa under `platforms/ios`.

```shell
kxbuild build .\platforms\ios\App.xcworkspace -c Release
```
