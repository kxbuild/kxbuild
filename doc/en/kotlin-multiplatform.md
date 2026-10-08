# Build the iOS app of a Kotlin Multiplatform project with kxbuild

**English** · [简体中文](../zh/kotlin-multiplatform.md)

kxbuild builds the iOS app of a Kotlin Multiplatform project on Windows, Linux or macOS. The `iosApp` project and your Gradle build scripts need no changes, and Compose Multiplatform works too.

## Steps

### 1. Set up your environment

Install the JDK and Android tooling as described in the Kotlin Multiplatform docs (for example, Android Studio with the Kotlin Multiplatform plugin).

### 2. Create a project

Skip this step for an existing project. Otherwise create one with the Kotlin Multiplatform wizard in Android Studio or on the JetBrains website, and select the iOS target.

### 3. Run doctor on the iOS project

Do this the first time you build a project. Doctor checks your Kotlin Multiplatform setup and prepares the iOS project. The first run downloads a few hundred MB and takes a while. Run it again after you upgrade Kotlin.

```shell
kxbuild doctor D:\work\mykmpapp\iosApp\iosApp.xcodeproj
```

### 4. Build and install on the iPhone

Set up [signing](../../README.md#-signing), connect the iPhone over USB, then run this in the project root. The Gradle task that builds the shared Kotlin framework runs as part of the build.

```shell
kxbuild build .\iosApp\iosApp.xcodeproj --install
```

### 5. Release

Build with the Release configuration to get an .ipa in the `iosApp` folder.

```shell
kxbuild build .\iosApp\iosApp.xcodeproj -c Release
```

## Troubleshooting

| Problem | Fix |
|---|---|
| A Run Script phase fails, for example because `JAVA_HOME` isn't set or Kotlin/Native isn't ready | Run `kxbuild doctor` with the iOS project path. It sets up what the Gradle build needs |
