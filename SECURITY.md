# Security Policy / 安全说明

Codex Pet manages Codex authentication files. Those files may contain access tokens, refresh tokens, ID tokens, or API keys, so they must be treated as sensitive credentials.

Codex Pet 会管理 Codex 登录文件。这些文件可能包含 access token、refresh token、ID token 或 API key，因此必须把它们视为敏感凭证。

## Credential storage / 凭证存储

The active Codex credential remains at `$CODEX_HOME/auth.json`, normally `~/.codex/auth.json`.

当前正在使用的 Codex 凭证保存在 `$CODEX_HOME/auth.json`，默认为 `~/.codex/auth.json`。

Codex Pet stores managed account snapshots and switch backups under:

- macOS and other Unix systems: `~/.codex-account-switcher/`
- Windows: the system configuration directory, normally `%APPDATA%\codex-account-switcher\`
- Custom location: `$CODEX_ACCOUNT_HOME`, when configured

主要内容包括：

- `accounts/<name>/auth.json`: each managed account snapshot / 每个受管账号的凭证快照
- `backups/*.auth.json`: copies made before account switches / 切换账号前创建的备份
- `accounts.json`: account aliases and timestamps; it is metadata, not the token store / 账号别名和时间戳等元数据，不是 Token 存储

On Unix, Codex Pet sets managed directories to mode `0700` and files written by the app to mode `0600`. On Windows, access is governed by the current user's filesystem ACLs.

在 Unix 系统上，Codex Pet 会将受管目录设为 `0700`，将应用写入的文件设为 `0600`。Windows 上的访问控制由当前用户的文件系统 ACL 决定。

### Important limitation / 重要限制

Credentials are stored as plaintext JSON for compatibility with Codex. Codex Pet does not encrypt them with Keychain, Credential Manager, or an application-specific password. A process running as the same OS user, an administrator, malware, or anyone with access to an unencrypted disk or backup may be able to read them.

为了与 Codex 兼容，凭证以明文 JSON 存储。Codex Pet 目前不使用 Keychain、Credential Manager 或应用专用密码对凭证加密。与你使用同一系统账号的进程、管理员、恶意软件，或能访问未加密磁盘及备份的人，都可能读取这些凭证。

Use full-disk encryption, lock your OS account, restrict backup access, and do not sync the storage directory through a public or shared cloud folder.

建议启用全盘加密、锁定系统账号、限制备份访问，不要将凭证目录同步到公开或共享的云盘中。

## What leaves the device / 网络请求

Codex Pet has no project-operated account storage or telemetry service. Credentials are not uploaded to a server operated by this project. Network access still occurs when it is necessary to use Codex:

Codex Pet 没有由本项目运营的账号存储或遥测服务，凭证不会上传到本项目运营的服务器。但在使用 Codex 功能时仍会发生必要的网络请求：

- Login starts the installed Codex client in an isolated temporary `CODEX_HOME`; Codex performs its normal authentication flow.
- Token and identity refreshes communicate with Codex/OpenAI authentication services. Identity refresh sends the refresh token to `https://auth.openai.com/oauth/token` over HTTPS.
- Quota checks and warm-up requests run the installed Codex app-server with a temporary credential copy. These operations contact Codex/OpenAI services; warm-up deliberately sends one lightweight request and consumes a small amount of quota.
- Update checks download the signed updater manifest and artifacts from the project's public GitHub Releases repository.

登录、刷新、额度查询和预热使用独立的临时目录。临时目录由操作系统临时文件机制管理，操作结束后会进行清理。预热会主动发送一次轻量请求，并消耗少量额度。

## UI and permission boundary / 界面与权限边界

Tokens and API keys are read by the Rust backend and are not returned to the webview. The UI receives only the account alias and derived status such as email, expiry, plan, quota, and operation errors. The bundled webview loads local application assets under a restrictive Content Security Policy.

Token 和 API key 由 Rust 后端读取，不会返回给 WebView。界面只接收账号别名、邮箱、过期时间、套餐、额度和操作错误等派生信息。打包的 WebView 在限制性 Content Security Policy 下加载本地应用资源。

This boundary reduces accidental exposure to the renderer, but it does not protect credentials after the Rust process or the user's OS account has been compromised.

该边界能减少凭证意外暴露给渲染层的风险，但无法在 Rust 进程或用户的系统账号已被攻破时继续保护凭证。

## Switching, deletion, and backups / 切换、删除与备份

Before a switch, Codex Pet backs up the active Codex credential and saves changes to the current managed account. It then refreshes the target snapshot when possible and atomically replaces the active `auth.json`. If refresh fails, the app warns the user and can continue with the existing target credential.

切换前，Codex Pet 会备份当前 Codex 凭证，并保存当前受管账号的变化。随后它会尝试刷新目标快照，并以原子写入方式替换当前 `auth.json`。如果刷新失败，应用会警告用户，但可以继续使用目标账号的现有凭证。

Deleting an account removes its managed `accounts/<name>/` snapshot, but it does **not** remove switch backups and does not revoke the credential at OpenAI. If the deleted account is active, the current `$CODEX_HOME/auth.json` may also remain on disk.

删除账号只会删除对应的 `accounts/<name>/` 快照，**不会**删除历史切换备份，也不会在 OpenAI 端撤销凭证。如果被删除的账号正在使用，当前 `$CODEX_HOME/auth.json` 也可能仍然存在。

To fully retire a credential, sign out or revoke it through the appropriate Codex/OpenAI account flow, then remove the relevant local snapshots and backups while Codex Pet and Codex are closed.

如需完全停用凭证，请先通过对应的 Codex/OpenAI 账号流程退出或撤销，然后在关闭 Codex Pet 和 Codex 后删除相关本地快照与备份。

## Reporting a vulnerability / 报告安全漏洞

Please use GitHub's private vulnerability reporting for this repository when it is available. Do not include tokens, API keys, complete `auth.json` files, or other personal data in a public issue.

如果仓库已启用 GitHub 私密漏洞报告，请优先通过该渠道报告。不要在公开 Issue 中附上 Token、API key、完整 `auth.json` 或其他个人数据。

If private reporting is unavailable, open a minimal public issue asking the maintainer for a private contact channel. Include only the affected version, platform, and a non-sensitive summary until a private channel has been established.

如果私密报告不可用，请只创建一个不含敏感细节的公开 Issue，请求维护者提供私密联系渠道。在私密渠道建立前，只提供受影响版本、操作系统和不含敏感信息的摘要。

The latest released version receives security fixes. Reports will be acknowledged as soon as practical, then investigated before public disclosure is coordinated.

安全修复会面向最新发布版本。维护者会尽快确认报告、开始调查，并在协调好修复与披露时间后公开细节。
