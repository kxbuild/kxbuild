# Build Swift iOS apps with kxbuild

**English** · [简体中文](../zh/swift.md)

kxbuild builds Swift iOS projects on Windows, Linux and macOS, signs them and installs them on your iPhone. SwiftUI, UIKit, Swift Package Manager dependencies and mixed Swift / Objective-C are all supported. Build settings, target dependencies and compiler flags are read by Xcode's rules, so your project builds as is.

Swift projects come in two kinds, [Xcode projects](#xcode) and [standalone Swift packages](#package) with only a Package.swift. For projects that use CocoaPods, see [CocoaPods](cocoapods.md).

<a id="xcode"></a>

## Xcode projects

A project with an .xcodeproj or .xcworkspace. Swift package dependencies are resolved automatically before each build.

### 1. Create a project

Skip this step for an existing project. Otherwise create one from the SwiftUI (`app_swiftui`) or UIKit (`app_swift`) template.

```shell
kxbuild project create -t app_swiftui -n MyApp -b com.example.myapp -o D:\projects
```

### 2. Run doctor

Doctor checks the build environment and fixes anything missing.

```shell
kxbuild doctor .\MyApp.xcodeproj
```

<a id="add-package"></a>

### 3. Add a Swift package dependency (optional)

The equivalent of Xcode's *Add Package Dependencies*. As in Xcode, a remote package defaults to its latest version and allows updates up to the next major version. Adding one needs a network connection.

**Remote package**

```shell
kxbuild project add-package -P .\MyApp.xcodeproj --url https://github.com/Alamofire/Alamofire.git --product Alamofire
```

**Local package**

```shell
kxbuild project add-package -P .\MyApp.xcodeproj --path ..\MyKit
```

Version rules: `--from`, `--exact`, `--up-to-next-minor`, `--range`, `--branch`, `--revision`. Choose the target that links the products with `-t`. Run `kxbuild project add-package --help` for all options.

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then build the Debug configuration and install it:

```shell
kxbuild build .\MyApp.xcodeproj --install
```

### 5. Release

Build with the Release configuration to get `build\MyApp.ipa` in the project folder, ready to upload to App Store Connect.

**.xcodeproj project**

```shell
kxbuild build .\MyApp.xcodeproj -c Release
```

**.xcworkspace workspace**

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

<a id="package"></a>

## Standalone Swift packages

A Swift package with only a Package.swift can't be packaged as an app on its own, and that's true on a Mac too.

- **The package is an app.** Its executable target holds a SwiftUI or UIKit app entry point. Follow the steps below to convert it to an app project.
- **The package only provides libraries** for apps to use. Add it to the app project as a dependency, as in [step 3](#add-package) above.

### 1. Compile-check

Run this in the folder containing Package.swift.

```shell
kxbuild build .
```

### 2. Convert to an app project

Converting adds a `MyApp` subfolder to the package folder with the app project inside. The app still uses the package's code, so when you edit the package, you edit the app. Commit this subfolder to version control along with the package.

```shell
kxbuild project spm2app . -n MyApp -b com.example.myapp
```

If the app uses a target from the same package that has no matching library product, the conversion adds a `.library` entry to `products` in Package.swift. **Review and commit that change.** When the package has several app executables, choose one with `--executable`.

### 3. Build and release

From here it's a normal Xcode project. The .ipa is written to `MyApp\build\MyApp.ipa`.

```shell
kxbuild build .\MyApp\MyApp.xcodeproj -c Release
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| A Debug build fails to link with `undefined symbol: …vpfi` | A known issue in the open-source Swift compiler. It happens with a property that has an initial value, such as `@State private var count = 0`, when the struct's init is in another file. Set the environment variable `KXBUILD_SWIFT_WHOLE_MODULE=all` (or add `-D KXBUILD_SWIFT_WHOLE_MODULE=all` to one build) and build again. Swift targets are then built as a whole module. |
| Swift packages won't download, or the build can't find a package | Run `kxbuild resolve .\MyApp.xcodeproj`. If that doesn't help, add `--force` to reset the package caches. |
| `scheme / target "…" not found` | Run `kxbuild project info .\MyApp.xcodeproj` to list the schemes and targets. |
