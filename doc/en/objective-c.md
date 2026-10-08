# Build Objective-C iOS apps with kxbuild

**English** · [简体中文](../zh/objective-c.md)

kxbuild builds Objective-C iOS projects on Windows, Linux and macOS, signs them and installs them on your iPhone. Your .xcodeproj and .xcworkspace build as is: build settings, target dependencies and compiler flags are read by Xcode's rules. Mixed Objective-C and Swift, and C / C++ sources, are supported. For projects that use CocoaPods, see [CocoaPods](cocoapods.md).

## Steps

### 1. Create a project

Skip this step for an existing project. Otherwise create one from the Objective-C template (`app_objc`), or `app_objc_swift` for a mixed Objective-C + Swift project.

```shell
kxbuild project create -t app_objc -n MyApp -b com.example.myapp -o D:\projects
```

### 2. Run doctor

Doctor checks the build environment and fixes anything missing.

```shell
kxbuild doctor .\MyApp.xcodeproj
```

### 3. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then:

```shell
kxbuild build .\MyApp.xcodeproj --install
```

### 4. Release

Build with the Release configuration to get `build\MyApp.ipa` in the project folder.

**.xcodeproj project**

```shell
kxbuild build .\MyApp.xcodeproj -c Release
```

**.xcworkspace workspace**

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

## Tips

- List schemes, targets and bundle IDs with `kxbuild project info .\MyApp.xcodeproj`, then choose one with `-s` or `-t`.
- Change a build setting for one build with `-D KEY=VALUE`, or in the project file with `kxbuild project add-setting`.
- Want code completion in your editor? `kxbuild build .\MyApp.xcodeproj --emit-commands-only` writes `compile_commands.json` for clangd without compiling anything.
