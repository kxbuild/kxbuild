# Reach a dev server on the computer over USB

**English** · [简体中文](../zh/dev-server.md)

Debug builds of many frameworks load their code at run time from a dev server on the computer: Metro for React Native and Expo, a livereload server for Cordova / Ionic, and so on. For the app to reach it, the phone and the computer usually have to be on the same network with the port allowed through the firewall, and in WSL the computer's address is the distro's internal one, which the phone cannot reach at all.

kxbuild moves that traffic to the USB cable: a Debug device build carries a small component that serves the dev server's ports on the phone's `127.0.0.1`, and `kxdevice reverse` carries those connections over the cable to the same ports on the computer. `kxbuild run` and the VS Code extension start `kxdevice reverse` and the dev server for you. The phone and the computer don't need to share a network, and the firewall stays as it is. When the USB path is not available, the component falls back to the computer's local-network address recorded at build time, as before.

React Native and Expo projects need no setup: kxbuild knows Metro. Any other framework takes three steps.

## 1. Tell kxbuild the ports and the command

Add these build settings to the project (an xcconfig file, the project's settings, or `-D` on the command line):

| Setting | Purpose | Example |
|---|---|---|
| `KXBUILD_DEV_SERVER_PORTS` | The ports the app reaches, comma separated, up to 8 | `8100,35729` |
| `KXBUILD_DEV_SERVER_COMMAND` | The command that starts the dev server. Without it kxbuild doesn't start one and asks you to | `ionic serve --livereload --port 8100` |
| `KXBUILD_DEV_SERVER_DIR` | The command's working directory; default: the project's directory | `$(SRCROOT)/..` |
| `KXBUILD_DEV_SERVER_OVER_USB` | `NO` turns this off | |

This applies to Debug device builds only; Release builds are untouched.

## 2. Point the app's dev-server address at 127.0.0.1

Wherever the framework reads the dev server's address, make it `127.0.0.1`. For Cordova that is `config.xml`:

```xml
<content src="http://127.0.0.1:8100/" />
```

kxbuild changes React Native's and Expo's `ip.txt` itself.

## 3. Run

```shell
kxbuild run .\ios\MyApp.xcworkspace
```

kxbuild builds, installs and launches the app, and starts `kxdevice reverse` and the dev server (a server already answering on the port is reused). In a terminal the dev server stays in that terminal until Ctrl-C; with `--json`, or from a script or an AI tool, both keep running in the background with logs under `~/.kxbuild/devserver/`, and the next `run` reuses them. `--no-dev-server` skips this.

Pressing F5 in VS Code does the same: the dev server runs in a terminal named Dev Server (Metro for React Native).

## Troubleshooting

| Problem | Fix |
|---|---|
| The app says it can't reach the dev server | Check the `kxbuild run` terminal or the logs under `~/.kxbuild/devserver/`: the dev server must be running and the iPhone connected over USB. Once the server is ready, reload the app |
| Switching between two Debug apps leaves one unable to connect | Rebuild both with the current kxbuild; the component in older builds doesn't hand over the ports promptly on an app switch |
| The dev server is slow in WSL | Keep the project under your Linux home (`~/`), not on a Windows drive under `/mnt/` |
