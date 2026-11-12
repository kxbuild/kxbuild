# Build Cocos Creator and cocos2d-x iOS games with kxbuild

**English** · [简体中文](../zh/cocos2d-x.md)

kxbuild builds the iOS version of a Cocos Creator 3.8 or Cocos 4 game, signs it and installs it on your iPhone. The engine builds along with your game, with no changes to the engine or the project settings. Older cocos2d-x 3.x and 4.x projects are supported too, see [cocos2d-x 3.x and 4.x](#cocos2d-x-3x-and-4x).

## Cocos Creator

### 1. Build the iOS platform with cocos-cli

The Cocos Creator editor has no iOS build option on Windows, so build the iOS platform with cocos-cli. Install it as described in the [cocos-cli repository](https://github.com/cocos/cocos-cli) (it needs Node.js 22); Creator 3.8 projects build with it as well.

In the project root, create `ios-build.json` and replace the package name with your own Bundle ID:

```json
{
  "platform": "ios",
  "debug": true,
  "packages": {
    "ios": {
      "packageName": "com.example.mygame",
      "skipUpdateXcodeProject": true
    }
  }
}
```

Then build from the project root:

```shell
cocos build --project . --platform ios --build-config ios-build.json
```

The first build ends with an error that the Xcode generator is missing. Your game's assets are built by then, so go on to step 2. If the game does not start from the first scene in the project, add `"startScene": "<scene uuid>"` to `ios-build.json`; the uuid is in the `.scene.meta` file next to the scene.

### 2. Run doctor on the project

Do this after the first build. Doctor generates the Xcode project in `build\ios\proj` and prints the build-and-install command on its last line. Run it again after you change native code in the `native` folder or the iOS build options.

```shell
kxbuild doctor D:\work\MyGame
```

### 3. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB and run the command doctor printed. When the project name has only letters, digits, `-` and `_`, the target is `<project name>-mobile`; otherwise it is `CocosGame`.

```shell
kxbuild build .\build\ios\proj\MyGame.xcodeproj -t MyGame-mobile --install
```

After you change scenes, scripts or assets in the Cocos Creator editor, run the `cocos build` command from step 1 and then this command again. You don't need to run doctor again.

### 4. Release

Change `"debug": true` to `"debug": false` in `ios-build.json`, run the `cocos build` command from step 1 again, then build with the Release configuration to get an .ipa.

```shell
kxbuild build .\build\ios\proj\MyGame.xcodeproj -c Release -t MyGame-mobile
```

Building with cocos-cli only reformats `package.json` and adds `settings/cocos.config.json` to a Creator 3.8 project. Review these two changes before you commit.

## cocos2d-x 3.x and 4.x

### 1. Install cocos2d-x and create a project

Skip this step for an existing project. Otherwise download the engine and its dependencies as described on the cocos2d-x website, and run `setup.py` in the engine folder to set up the `cocos` command. Then create the project.

```shell
cocos new MyGame -l cpp -p com.example.mygame -d .
```

### 2. Run doctor on the project

Do this the first time you build a project. Doctor checks your cocos2d-x setup and prepares the iOS project. For 4.x, this step also generates the Xcode project in the `build-xcode` folder.

```shell
kxbuild doctor D:\work\MyGame
```

### 3. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then run the command for your engine version in the project root.

**3.x project**

```shell
kxbuild build .\proj.ios_mac\MyGame.xcodeproj -t MyGame-mobile --install
```

**4.x project**

```shell
kxbuild build .\build-xcode\MyGame.xcodeproj -t MyGame --install
```

### 4. Release

Build with the Release configuration to get an .ipa.

**3.x project**

```shell
kxbuild build .\proj.ios_mac\MyGame.xcodeproj -c Release -t MyGame-mobile
```

**4.x project**

```shell
kxbuild build .\build-xcode\MyGame.xcodeproj -c Release -t MyGame
```
