# Build Godot iOS exports with kxbuild

**English** · [简体中文](../zh/godot.md)

The iOS project Godot exports can be built with kxbuild on Windows, Linux or macOS and installed on your iPhone. Export from Godot as usual and build the export folder like any Xcode project. Your Godot project settings stay as they are.

## Steps

### 1. Export an Xcode project from Godot

Develop your project in Godot as usual. Download the export templates from **Editor > Manage Export Templates**, add an iOS preset in **Project > Export**, fill in App Store Team ID and Bundle Identifier, and export to an empty folder outside the Godot project with a file name such as `MyGame.ipa`. On Windows, Godot writes the Xcode project `MyGame.xcodeproj` instead of an .ipa.

### 2. Run doctor

Doctor checks the build environment and fixes anything missing.

```shell
kxbuild doctor .\MyGame.xcodeproj
```

### 3. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then:

```shell
kxbuild build .\MyGame.xcodeproj --install
```

### 4. Release

Build with the Release configuration to get an .ipa in the export folder.

```shell
kxbuild build .\MyGame.xcodeproj -c Release
```

After you change GDScript or scenes, export again to the same folder and rebuild.
