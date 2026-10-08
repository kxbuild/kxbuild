# Build CocoaPods iOS projects with kxbuild

**English** · [简体中文](../zh/cocoapods.md)

iOS projects that use CocoaPods keep managing dependencies with `pod`, and kxbuild builds the workspace that `pod install` generates, on Windows, Linux or macOS. CocoaPods is installed along with kxbuild, so `pod` runs in any terminal. Your Podfile and project need no changes, whether the project is Swift or Objective-C.

## Steps

### 1. Run doctor

Doctor checks CocoaPods and installs it if it's missing.

```shell
kxbuild doctor
```

When it finishes, open a new terminal. If this prints a version number, you're ready.

```shell
pod --version
```

### 2. Create a project

For a new project, use the template that comes with a Podfile. It runs `pod install` after creating the project, so you can skip to step 4.

```shell
kxbuild project create -t app_swift_cocoapods -n MyApp -b com.example.myapp -o D:\projects
```

If an existing project has no Podfile yet, create one in the folder containing the .xcodeproj. If it already has one, go to the next step.

```shell
pod init
```

### 3. Add dependencies

List the libraries each target needs in the Podfile. The version syntax is in the appendix at the end of this page.

```ruby
platform :ios, '15.0'
use_frameworks!

target 'MyApp' do
  pod 'Alamofire', '~> 5.9'
  pod 'SDWebImage'
end
```

Then install from the folder containing the Podfile. This downloads the dependencies and creates the Pods folder, Podfile.lock and MyApp.xcworkspace.

```shell
pod install
```

From now on, build the **.xcworkspace**. The .xcodeproj can't find the libraries in Pods. Run `pod install` again every time you change the Podfile, and commit Podfile.lock so everyone on the team installs the same versions.

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then:

```shell
kxbuild build .\MyApp.xcworkspace --install
```

### 5. Release

Build the workspace with the Release configuration to get `build\MyApp.ipa` in the project folder.

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

## Update dependencies

`pod install` only installs the versions recorded in Podfile.lock and never upgrades libraries you already have. To upgrade, first see which libraries have newer versions, then update all of them or just the ones you name.

```shell
pod outdated
pod update Alamofire
```

`pod update` without a library name upgrades every library within the ranges your Podfile allows. If a version that was just released can't be found, add `--repo-update` to refresh the local spec index first.

## Troubleshooting

| Problem | Fix |
|---|---|
| The build can't find headers or modules from Pods | You're building the .xcodeproj. Build the .xcworkspace instead. If you just changed the Podfile, run `pod install` first |
| `The sandbox is not in sync with the Podfile.lock` | Podfile.lock changed when you pulled. Run `pod install` |
| Dependencies are in a bad state and you want to start over | Delete the Pods folder and run `pod install` again. If that doesn't help, clear the cache with `pod cache clean --all` |
| The `pod` command isn't found | Run `kxbuild doctor`, then open a new terminal |

## Appendix: pod commands and version syntax

| Command | What it does |
|---|---|
| `pod init` | Creates a Podfile for the project in the current folder |
| `pod install` | Installs dependencies from the Podfile and Podfile.lock and creates the .xcworkspace |
| `pod update [name]` | Upgrades all libraries or the named one, and updates Podfile.lock |
| `pod outdated` | Lists libraries that have newer versions |
| `pod repo update` | Refreshes the local spec index |
| `pod search keyword` | Searches for available libraries |
| `pod cache clean --all` | Clears the download cache |
| `pod deintegrate` | Removes CocoaPods from the project |

| Podfile entry | Meaning |
|---|---|
| `pod 'Alamofire'` | Latest version |
| `pod 'Alamofire', '5.9.1'` | Exactly this version |
| `pod 'Alamofire', '~> 5.9'` | 5.9 or later, below 6.0 |
| `pod 'Alamofire', '>= 5.0'` | 5.0 or later |
| `pod 'MyKit', :path => '../MyKit'` | A library in a local folder |
| `pod 'MyKit', :git => 'https://…/MyKit.git', :tag => '1.0.0'` | A specific version from a Git repository |
