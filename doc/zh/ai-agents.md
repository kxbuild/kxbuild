# 用 AI 编码代理和 kxbuild 开发 iOS 应用

[English](../en/ai-agents.md) · **简体中文**

AI 编码代理——Claude Code、GitHub Copilot、Cursor、Codex、Windsurf——负责写代码；kxbuild 让它像你一样验证结果：构建应用、装到你的 iPhone 上、启动、读日志、截图、查看视图层级、逐个屏幕点按、用断点调试。这一切在 Windows、Linux 和 macOS 上都能做。

接入代理有两种方式，可以同时用。

## 1. MCP 服务器（推荐）

`kxbuild mcp` 和 `kxdevice mcp` 通过 stdio 提供 Model Context Protocol。在代理里注册一次，每个动作就都成了它的一个工具。

**Claude Code**

```
claude mcp add kxbuild -- kxbuild mcp
claude mcp add kxdevice -- kxdevice mcp
```

**Cursor、Windsurf、Codex 和其他 MCP 客户端**——把下面这段加进客户端的 MCP 配置（`.cursor/mcp.json`、`.mcp.json` 等）：

```json
{
  "mcpServers": {
    "kxbuild": { "command": "kxbuild", "args": ["mcp"] },
    "kxdevice": { "command": "kxdevice", "args": ["mcp"] }
  }
}
```

**装了 KXBuild 插件的 VS Code** 会自动把两个服务器注册给 Copilot 的代理模式；在 Cursor 里插件同样会注册。插件还提供 *KXBuild: 复制给 AI 工具用的 MCP 服务器配置*，把上面这段 JSON（带安装路径）放到剪贴板。

代理随后能做的事：

| 服务器 | 工具 |
| --- | --- |
| `kxbuild` | `project_info`、`build_project`、`run_app`（构建 → 安装 → 启动，可选收集日志和截图）、`test_project`、`create_project`、`list_templates`、`resolve_packages`、`doctor`、`license_status` |
| `kxdevice` | `list_devices`、`list_apps`、`install_app`、`launch_app`、`kill_app`、`screenshot`、`view_hierarchy`、`logs`、`syslog`、`crash_logs`、`ui_tap` / `ui_swipe` / `ui_type` / `ui_find` / `ui_source`、`debug_launch` / `debug_breakpoint` / `debug_continue` / `debug_backtrace` / `debug_variables` / `debug_evaluate` / `debug_output` / `debug_stop`、`forward_port` |

然后直接提要求：*“把应用构建并装到我的 iPhone 上运行，等十秒后给我截图和日志”*、*“在 `ContentView.body` 下个断点，运行，停下来时告诉我变量的值”*、*“点一下登录按钮，看看屏幕上显示了什么”*。

只读的工具（设备、日志、截图、视图层级）带有只读标记，多数客户端不会弹确认就执行。工具失败时会返回原因和该做的那一件事；例如没有授权时会提示执行 `kxbuild license activate <激活码>` 或 `kxbuild license trial`。

## 2. 命令行加 `--json`

kxbuild 和 kxdevice 的每条命令都支持 `--json`：stdout 变成一行一个 JSON 事件，靠执行 shell 命令工作的代理读到的是结果，不用解析文本。

```
kxbuild build MyApp.xcodeproj --json --no-ipa
kxbuild run MyApp.xcodeproj --json --wait-logs 10s --screenshot build/run.png --view-hierarchy build/views.json
kxbuild test MyApp.xcodeproj --json
```

- 最后一行 `{"type":"result"}` 带 `data.success`；编译问题以 `{"type":"diagnostic"}` 事件给出，含文件、行、列、级别和消息。
- `kxbuild run` 依次构建、准备设备、安装、启动，并（带上面的参数时）收集应用日志、截图和视图层级，然后返回，应用继续运行。结果里写明走到了哪一步，失败时有 `failure.reason` 和 `failure.hint`。
- 退出码说明失败类别：2 用法错误，3 授权，4 没有可用设备，5 构建失败，6 测试失败，7 安装或启动失败。脚本和代理按它分支，不用解析任何输出。
- 日志有边界：`kxdevice ostrace --pid <pid> --duration 10s --count 500 --json` 会自己结束并说明原因。

## 3. 让代理了解项目：AGENTS.md

用 `kxbuild project create` 创建的项目自带 `AGENTS.md`，告诉代理要构建哪个容器、上面这些命令、怎么读结果、怎么配置 MCP 服务器。代理会自己去读它（Claude Code、Codex、Cursor、Copilot 都会找这个文件）。

已有项目可以在根目录新建 `AGENTS.md`，至少写上：

```markdown
# Working on MyApp with an AI coding agent

This iOS project is built with KXBuild on Windows, Linux and macOS; use kxbuild instead of xcodebuild.

- Build container: `MyApp.xcworkspace`
- Build: `kxbuild build MyApp.xcworkspace --json --no-ipa`
- Run on the connected iPhone with logs and a screenshot: `kxbuild run MyApp.xcworkspace --json --wait-logs 10s --screenshot build/run.png`
- Tests: `kxbuild test MyApp.xcworkspace --json`
- App Store .ipa: `kxbuild build MyApp.xcworkspace -c Release --export app-store --json`
- Read `data.success`, the `diagnostic` events and `data.failure.hint`; exit codes: 3 license, 4 no device, 5 build failed, 7 install or launch failed.
- Project edits go through `kxbuild project add-package | add-resource | add-target | settings`, never through project.pbxproj by hand.
- MCP: `claude mcp add kxbuild -- kxbuild mcp` and `claude mcp add kxdevice -- kxdevice mcp`.
```

## 4. 在 VS Code 里

装了 KXBuild 插件后，Copilot 的代理模式还能直接用编辑器本身：`#kxbuildContext`（选中的设备、启动项目、配置）、`#kxbuildBuild` 和 `#kxbuildRun`（进度和“问题”面板和你点运行时一样）、`#kxbuildProblems`、`#kxbuildConsole`（应用输出），以及调试暂停时的 `#kxbuildThreads`、`#kxbuildStack`、`#kxbuildVariables`、`#kxbuildEvaluate`。*KXBuild: 运行（不调试）* 会构建、安装、启动后立即返回，和代理运行应用的方式一样；*KXBuild: 停止应用* 结束它。

## 常见问题

**代理能在哪些系统上用？** Windows、Linux 和 macOS 都可以。构建、签名、安装、截图、日志、UI 自动化和调试器都直接对 USB 连接的 iPhone 操作。

**应用要满足什么条件？** 开发签名的构建（项目 `keychain/` 文件夹里放 `.p12` 证书和 `.mobileprovision` 描述文件），以及已经信任这台电脑的 iPhone；UI 工具还会用到 kxdevice 首次使用时安装的 WebDriverAgent。

**两个代理能同时构建吗？** 个人授权下一台机器同一时间只跑一个构建；第二个调用会被明确拒绝并说明原因，代理等第一个结束后重试即可。
