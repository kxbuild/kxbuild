# Build Unity iOS exports with kxbuild

**English** · [简体中文](../zh/unity.md)

kxbuild builds the iOS project that Unity exports on Windows, Linux or macOS, signs it and installs it on your iPhone. Export from Unity as usual and treat the export folder as a regular Xcode project. No Unity plugin and no change to your Unity project settings are needed.

## Steps

### 1. Export an Xcode project from Unity

Develop your project in Unity as usual. With the iOS Build Support module installed, switch the platform to iOS in Build Settings and build to a folder.

### 2. Run doctor

Doctor checks the build environment and fixes anything missing.

```shell
kxbuild doctor .\Unity-iPhone.xcodeproj
```

If you use plugins with iOS dependencies, such as Firebase or Google Mobile Ads, the export folder contains a Podfile. In that case, run `pod install` in the export folder afterwards. See [CocoaPods](cocoapods.md).

### 3. Build and install on the iPhone

Set up [signing](../../README.md#-signing) and connect the iPhone over USB. The target is always `Unity-iPhone`.

**No Podfile**

```shell
kxbuild build .\Unity-iPhone.xcodeproj -t Unity-iPhone --install
```

**Podfile, after `pod install`**

```shell
kxbuild build .\Unity-iPhone.xcworkspace -t Unity-iPhone --install
```

### 4. Release

Build with the Release configuration to get an .ipa in the export folder.

**No Podfile**

```shell
kxbuild build .\Unity-iPhone.xcodeproj -c Release -t Unity-iPhone
```

**Podfile, after `pod install`**

```shell
kxbuild build .\Unity-iPhone.xcworkspace -c Release -t Unity-iPhone
```

After you change your Unity project, export again to the same folder and rebuild.

## Tips

- On Windows, keep the export folder in a short path such as `D:\proj`. Unity exports nest deeply.
