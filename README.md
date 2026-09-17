# Codex Pet · Codex Account Switcher

> 把多个 Codex 账号、登录状态和额度放进一只轻量的桌面宠物里。无需手动替换 `auth.json`，一键切换后即可继续使用 Codex。
>
> Manage multiple Codex accounts, sign-in status, and usage limits from a lightweight desktop pet. Switch accounts without manually replacing `auth.json`.
# Support the Project / 支持作者​
> 如果这个小工具帮到了你，欢迎扫码打赏支持，让这只桌面宠物继续成长 🐾
> 
> If this tool makes your life easier, feel free to support its development.

> Alipay / 支付宝​


<p align="center">
  <img src="https://github.com/cyilang17/codex-switch-desktop/raw/main/release-assets/screenshots/codex-pet-account-panel.png" alt="Codex Pet account panel showing multiple accounts and usage limits" width="220">
  &nbsp;&nbsp;
  <img height="220" alt="image" src="https://github.com/user-attachments/assets/ecdd614c-09ad-4b41-9c0a-fd4e9cd57195" width="220"/>
  <img alt="image" src="https://github.com/cyilang17/codex-account-switcher/blob/main/AliPay.JPG?raw=true" width="220"/>
</p>

<p align="center">
  <a href="https://github.com/cyilang17/codex-account-switcher/releases/latest"><strong>下载 macOS 版</strong></a>
  ·
  <a href="https://github.com/cyilang17/codex-account-switcher/releases/latest"><strong>下载 Windows 版</strong></a>
  ·
  <a href="https://github.com/cyilang17/codex-account-switcher/releases"><strong>查看全部版本</strong></a>
</p>

<p align="center">
  <a href="https://github.com/cyilang17/codex-account-switcher/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/cyilang17/codex-account-switcher?display_name=tag&sort=semver"></a>
  <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue.svg">
  <img alt="Platforms: macOS and Windows" src="https://img.shields.io/badge/platform-macOS%20%7C%20Windows-lightgrey">
</p>

## 功能 Features

- 多账号登录、别名管理和一键切换 / Multiple accounts with aliases and one-click switching
- 切换账号后自动重启 ChatGPT / Automatically restarts ChatGPT after switching
- 显示登录状态有效期、套餐和订阅到期时间 / Shows login-session expiry, plan, and subscription expiry
- 按接口实际返回显示最多两个额度窗口（如 5 小时、每周或每月）及重置时间 / Shows up to two quota windows returned by the API (such as five-hour, weekly, or monthly) with reset times
- 可为非当前账号发起轻量预热请求，无需切换全局账号 / Warms up an inactive account without switching the global account
- 支持手动刷新登录状态、额度和订阅信息 / Manually refreshes login status, quota, and subscription data
- 桌面宠物模式，可拖动、置顶并覆盖全屏窗口 / Draggable pet mode with always-on-top support
- 中英文界面，默认跟随系统语言 / Chinese and English UI, following the system language by default
- macOS 和 Windows 安装包 / macOS and Windows installers

## 下载 Download

请前往 [Latest Release](https://github.com/cyilang17/codex-account-switcher/releases/latest) 下载对应平台的安装包。

Download the installer for your platform from the [latest release](https://github.com/cyilang17/codex-account-switcher/releases/latest).

## macOS 安装 macOS installation

当前版本没有 Apple Developer ID 签名和公证。将 DMG 中的 `Codex Pet.app` 拖入“应用程序”后，如果 macOS 提示应用已损坏或无法验证开发者，请在“终端”执行一次：

This macOS build is not signed or notarized with an Apple Developer ID. After dragging `Codex Pet.app` from the DMG into Applications, macOS may report that the app is damaged or cannot verify the developer. Open Terminal and run this command once:

```bash
xattr -dr com.apple.quarantine "/Applications/Codex Pet.app"
```

然后重新打开应用 / Then open the app again.

如果应用安装在其他位置，请将命令中的路径替换为实际的 `.app` 路径。

If the app is installed elsewhere, replace the path with the actual `.app` path.

## Windows 安装 Windows installation

Windows x64 版复用已安装 Codex 客户端在 `%LOCALAPPDATA%\\OpenAI\\Codex\\bin` 中的后台程序，不再把完整 Codex CLI 打进安装包。请先安装并启动一次 Codex 客户端；无需另装 Node.js/npm 版 CLI。

The Windows x64 build reuses the backend installed by the Codex desktop app under `%LOCALAPPDATA%\\OpenAI\\Codex\\bin` instead of bundling the full Codex CLI. Open the Codex app once before using the switcher; the Node.js/npm CLI is not required.

运行 Release 中的 NSIS `.exe` 安装器即可。如果 Windows SmartScreen 警告文件来源，请确认文件来自本项目 Release 页面，然后选择“更多信息 → 仍要运行”。

Run the NSIS `.exe` installer from the Release page. If Windows SmartScreen shows a warning, verify that the file came from this project's Release page, then choose “More info → Run anyway”.

## 开发 Development

```bash
npm install
npm start
```

需要先安装 Node.js、Rust 和 Tauri 所需的系统依赖。

Node.js, Rust, and the system dependencies required by Tauri must be installed first.

## 本地打包 Build locally

```bash
npm run dist:mac
npm run dist:win
```

macOS 应在 macOS 主机打包，Windows 应在 Windows 主机或 Windows CI 中打包。产物位于 `src-tauri/target/release/bundle/`。

Build macOS packages on macOS and Windows packages on Windows or Windows CI. Artifacts are written to `src-tauri/target/release/bundle/`.

## 自动更新 Automatic updates

应用会从公开仓库的 `latest.json` 检查新版本。用户确认后，应用会下载、校验、安装并重启。

The app checks `latest.json` in the public repository for updates. After confirmation, it downloads, verifies, installs, and restarts.

更新地址 / Update endpoint:

```text
https://github.com/cyilang17/codex-account-switcher/releases/latest/download/latest.json
```

## 数据与安全 Data and security

- 当前 Codex 登录文件：`$CODEX_HOME/auth.json`，默认是 `~/.codex/auth.json` / Current Codex auth file: `$CODEX_HOME/auth.json`, usually `~/.codex/auth.json`
- 账号快照保存在本机，不会上传到本项目运营的服务器 / Account snapshots stay on your device and are not uploaded to a server operated by this project
- Token 只在 Tauri Rust 后端读取，界面只接收邮箱和过期时间 / Tokens are read only by the Tauri Rust backend; the UI receives only emails and expiry times
- 预热使用独立的临时 `CODEX_HOME`，会消耗少量 Codex 额度，但不会替换当前登录 / Warm-up uses an isolated temporary `CODEX_HOME` and a small amount of Codex quota without replacing the active login
- 快照是本地明文凭证，应像对待原始 `auth.json` 一样保护系统账号、磁盘和备份 / Snapshots contain plaintext credentials; protect your OS account, disk, and backups as you would the original `auth.json`

完整的存储位置、网络请求、权限边界和漏洞报告方式请参阅 [SECURITY.md](SECURITY.md)。

See [SECURITY.md](SECURITY.md) for storage locations, network activity, permission boundaries, and vulnerability reporting.

## License

MIT
