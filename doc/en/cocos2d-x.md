# Build cocos2d-x iOS games with kxbuild

**English** · [简体中文](../zh/cocos2d-x.md)

kxbuild builds the iOS version of a cocos2d-x game on Windows, Linux or macOS, signs it and installs it on your iPhone. Both 3.x and 4.x are supported. The engine source builds along with your game as a subproject, with no changes to the engine or the project settings.

## Steps

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
