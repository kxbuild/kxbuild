# Develop iOS apps with AI coding agents and kxbuild

**English** · [简体中文](../zh/ai-agents.md)

An AI coding agent — Claude Code, GitHub Copilot, Cursor, Codex, Windsurf — writes the code; kxbuild lets it verify the result the way you would: build the app, install it on your iPhone, launch it, read its logs, take a screenshot, inspect the view hierarchy, tap through the screens and debug with breakpoints. All of this works on Windows, Linux and macOS.

There are two ways to connect an agent, and they combine.

## 1. MCP servers (recommended)

`kxbuild mcp` and `kxdevice mcp` serve the Model Context Protocol over stdio. Register them once with your agent and it gets a tool for each action.

**Claude Code**

```
claude mcp add kxbuild -- kxbuild mcp
claude mcp add kxdevice -- kxdevice mcp
```

**Cursor, Windsurf, Codex and other MCP clients** — add this to the client's MCP configuration (`.cursor/mcp.json`, `.mcp.json`, …):

```json
{
  "mcpServers": {
    "kxbuild": { "command": "kxbuild", "args": ["mcp"] },
    "kxdevice": { "command": "kxdevice", "args": ["mcp"] }
  }
}
```

**VS Code with the KXBuild extension** registers both servers with Copilot's agent mode automatically; in Cursor the extension registers them too. The extension also offers *KXBuild: Copy MCP Server Configuration for AI Tools*, which puts the JSON above (with the installed paths) on the clipboard.

What the agent can then do:

| Server | Tools |
| --- | --- |
| `kxbuild` | `project_info`, `build_project`, `run_app` (build → install → launch, optionally logs and a screenshot), `test_project`, `create_project`, `list_templates`, `resolve_packages`, `doctor`, `license_status` |
| `kxdevice` | `list_devices`, `list_apps`, `install_app`, `launch_app`, `kill_app`, `screenshot`, `view_hierarchy`, `logs`, `syslog`, `crash_logs`, `ui_tap` / `ui_swipe` / `ui_type` / `ui_find` / `ui_source`, `debug_launch` / `debug_breakpoint` / `debug_continue` / `debug_backtrace` / `debug_variables` / `debug_evaluate` / `debug_output` / `debug_stop`, `forward_port` |

Then ask for what you want: *"Build and run the app on my iPhone, wait ten seconds and show me a screenshot and the logs"*, *"Set a breakpoint in `ContentView.body`, run, and tell me the values when it stops"*, *"Tap the Login button and check what the screen shows"*.

Tools that only read (devices, logs, screenshots, the view hierarchy) are marked read-only, so most clients run them without asking. A tool that fails returns the reason and the one thing to do about it; a missing license, for example, asks for `kxbuild license activate <code>` or `kxbuild license trial`.

## 2. The command line with `--json`

Every kxbuild and kxdevice command accepts `--json`: stdout becomes one JSON event per line, so an agent that runs shell commands reads results instead of parsing text.

```
kxbuild build MyApp.xcodeproj --json --no-ipa
kxbuild run MyApp.xcodeproj --json --wait-logs 10s --screenshot build/run.png --view-hierarchy build/views.json
kxbuild test MyApp.xcodeproj --json
```

- The last `{"type":"result"}` line carries `data.success`; compiler problems arrive as `{"type":"diagnostic"}` events with file, line, column, severity and message.
- `kxbuild run` builds, prepares the device, installs, launches and (with the flags above) collects the app's logs, a screenshot and the view hierarchy, then returns while the app keeps running. Its result says which step it reached and, on failure, `failure.reason` and `failure.hint`.
- The exit code names the class of failure: 2 usage, 3 license, 4 no usable device, 5 build failed, 6 tests failed, 7 install or launch failed. Scripts and agents branch on it without parsing anything.
- Logs are bounded: `kxdevice ostrace --pid <pid> --duration 10s --count 500 --json` ends on its own and reports why.

## 3. Tell the agent about the project: AGENTS.md

Projects created with `kxbuild project create` contain an `AGENTS.md` that tells an agent which container to build, the commands above, how to read their results and how to set up the MCP servers. Agents read it on their own (Claude Code, Codex, Cursor and Copilot all look for it).

For an existing project, create `AGENTS.md` in the project root with at least:

```markdown
# Working on MyApp with an AI coding agent

This iOS project is built with KXBuild on Windows, Linux and macOS; use kxbuild instead of xcodebuild.

- Build container: `MyApp.xcworkspace`
- Build: `kxbuild build MyApp.xcworkspace --json --no-ipa`
- Run on the connected iPhone with logs and a screenshot: `kxbuild run MyApp.xcworkspace --json --wait-logs 10s --screenshot build/run.png`
- Tests: `kxbuild test MyApp.xcworkspace --json`
- Read `data.success`, the `diagnostic` events and `data.failure.hint`; exit codes: 3 license, 4 no device, 5 build failed, 7 install or launch failed.
- Project edits go through `kxbuild project add-package | add-resource | add-target | settings`, never through project.pbxproj by hand.
- MCP: `claude mcp add kxbuild -- kxbuild mcp` and `claude mcp add kxdevice -- kxdevice mcp`.
```

## 4. Inside VS Code

With the KXBuild extension, Copilot's agent mode can also use the editor itself: `#kxbuildContext` (selected device, startup project, configuration), `#kxbuildBuild` and `#kxbuildRun` (progress and Problems panel as when you click Run), `#kxbuildProblems`, `#kxbuildConsole` (the app's output) and, in a paused debug session, `#kxbuildThreads`, `#kxbuildStack`, `#kxbuildVariables` and `#kxbuildEvaluate`. *KXBuild: Run (no debugger)* builds, installs and launches the app and returns at once, the way an agent runs it; *KXBuild: Stop App* ends it.

## Common questions

**Which systems can the agent run on?** Windows, Linux and macOS. Building, signing, installing, screenshots, logs, UI automation and the debugger all work directly against the iPhone connected over USB.

**What does the app need?** A development-signed build (a `.p12` certificate and a `.mobileprovision` profile in the project's `keychain/` folder) and an iPhone that has trusted the computer; the UI tools additionally use the WebDriverAgent runner that kxdevice installs on first use.

**Can two agents build at the same time?** One build at a time per machine on a personal license; the second call is refused with a clear reason and the agent retries when the first one is done.
