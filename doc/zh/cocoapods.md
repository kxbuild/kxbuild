# 用 kxbuild 构建 CocoaPods iOS 工程

[English](../en/cocoapods.md) · **简体中文**

使用 CocoaPods 的 iOS 工程继续用 `pod` 管理依赖，kxbuild 在 Windows、Linux 或 macOS 上构建 `pod install` 生成的工作区。CocoaPods 随 kxbuild 一起安装，任何终端里都能直接使用 `pod`。无论是 Swift 还是 Objective-C 工程，Podfile 和工程都无需修改。

## 步骤

### 1. 运行 doctor

doctor 会检查 CocoaPods，缺少时自动安装。

```shell
kxbuild doctor
```

完成后新开一个终端，下面的命令能输出版本号即可。

```shell
pod --version
```

### 2. 创建工程

新工程可以用自带 Podfile 的模板，创建后会自动执行 `pod install`，然后直接跳到第 4 步。

```shell
kxbuild project create -t app_swift_cocoapods -n MyApp -b com.example.myapp -o D:\projects
```

已有工程还没有 Podfile 时，在 .xcodeproj 所在目录创建一个；已经有 Podfile 的直接进入下一步。

```shell
pod init
```

### 3. 添加依赖

在 Podfile 里写明每个 Target 需要的库，版本写法见本页末尾的附录。

```ruby
platform :ios, '15.0'
use_frameworks!

target 'MyApp' do
  pod 'Alamofire', '~> 5.9'
  pod 'SDWebImage'
end
```

然后在 Podfile 所在目录安装。这一步会下载依赖，并生成 Pods 目录、Podfile.lock 和 MyApp.xcworkspace。

```shell
pod install
```

之后一律构建 **.xcworkspace**，.xcodeproj 找不到 Pods 里的库。每次修改 Podfile 后都要重新执行 `pod install`；请把 Podfile.lock 提交到版本库，保证团队成员安装的版本一致。

### 4. 构建并安装到 iPhone

配置好[签名](../../README_zh.md#-签名)，用 USB 连接 iPhone，然后执行：

```shell
kxbuild build .\MyApp.xcworkspace --install
```

### 5. 发布

用 Release 配置构建工作区，在工程目录下得到 `build\MyApp.ipa`。

```shell
kxbuild build .\MyApp.xcworkspace -c Release
```

## 更新依赖

`pod install` 只安装 Podfile.lock 中记录的版本，不会升级已有的库。要升级时，先查看哪些库有新版本，再全部更新或只更新指定的库。

```shell
pod outdated
pod update Alamofire
```

`pod update` 不带库名时，会在 Podfile 允许的范围内升级所有库。刚发布的新版本找不到时，加 `--repo-update` 先刷新本地索引。

## 常见问题

| 问题 | 解决办法 |
|---|---|
| 构建时找不到 Pods 里的头文件或模块 | 你构建的是 .xcodeproj，请改为构建 .xcworkspace。刚改过 Podfile 的话先执行 `pod install` |
| `The sandbox is not in sync with the Podfile.lock` | 拉取代码后 Podfile.lock 变了，执行 `pod install` |
| 依赖状态混乱，想从头再来 | 删除 Pods 目录后重新 `pod install`；仍不行时用 `pod cache clean --all` 清除缓存 |
| 找不到 `pod` 命令 | 执行 `kxbuild doctor`，然后新开一个终端 |

## 附录：pod 命令与版本写法

| 命令 | 作用 |
|---|---|
| `pod init` | 为当前目录的工程创建 Podfile |
| `pod install` | 按 Podfile 和 Podfile.lock 安装依赖并生成 .xcworkspace |
| `pod update [库名]` | 升级全部或指定的库，并更新 Podfile.lock |
| `pod outdated` | 列出有新版本的库 |
| `pod repo update` | 刷新本地索引 |
| `pod search 关键字` | 搜索可用的库 |
| `pod cache clean --all` | 清除下载缓存 |
| `pod deintegrate` | 从工程中移除 CocoaPods |

| Podfile 写法 | 含义 |
|---|---|
| `pod 'Alamofire'` | 最新版本 |
| `pod 'Alamofire', '5.9.1'` | 固定为该版本 |
| `pod 'Alamofire', '~> 5.9'` | 5.9 及以上、6.0 以下 |
| `pod 'Alamofire', '>= 5.0'` | 5.0 及以上 |
| `pod 'MyKit', :path => '../MyKit'` | 本地目录中的库 |
| `pod 'MyKit', :git => 'https://…/MyKit.git', :tag => '1.0.0'` | Git 仓库中的指定版本 |
