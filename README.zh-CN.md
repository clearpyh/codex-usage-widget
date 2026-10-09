# Codex Usage Widget

[English](README.md) | 简体中文

一款本地运行的 Windows 桌面小组件，让你随时查看 ChatGPT Codex 用量限制。工作时可常驻显示 5 小时、每周剩余额度、重置时间和可用的重置次数。

> 独立社区项目，与 OpenAI 无关联，未获其认可或支持。

## 功能一览

- **蓝色表示剩余。** 蓝色区域显示剩余额度；灰色已用区域从左向右增长。
- **实时读取账户限制。** 通过本机 Codex CLI app server 获取账户返回的用量窗口。
- **桌面常驻。** 小组件可移动、置顶；也可切换为任务栏标签和托盘图标。
- **重置信息。** 显示各用量窗口的重置时间；Codex 返回时也显示可用重置次数。
- **默认仅本机运行。** 不需要额外账号或 API Key，也没有遥测和远程用量后端。
- **支持中英文。** 可使用系统、浅色或深色主题。

![进度条示意：灰色已用额度从左向右增长，蓝色剩余额度缩短](docs/images/remaining-quota-bar.svg)

## 显示内容

- 账户当前可用的用量周期及剩余百分比、重置时间，通常包括 5 小时和 7 天周期。
- 可选择在小组件标题、任务栏标签和托盘图标中显示的用量限制。
- 账户额度余额、消费限制、可用重置次数及到期时间（取决于 Codex 返回的数据）。
- 可选的 Token 活动和模型专属限制（如果 Codex 返回）。Token 活动仅供参考，不能直接换算为订阅余量。
- 可选的 Codex 运行状态点；需要用户主动安装并信任生命周期钩子。

小组件每两分钟自动刷新，也支持手动刷新。不同账户可用的周期可能不同；程序不会猜测或伪造缺失的用量窗口。

## 运行要求

- Windows 10 版本 1809 或更高版本（Release 版本支持 Windows 10/11 x64）。
- 已安装 Codex CLI，并使用要查看用量的 ChatGPT 账户完成登录。
- Codex CLI 可通过 `PATH` 找到，或在 `CODEX_USAGE_WIDGET_CODEX_PATH` 中配置完整路径。

在 PowerShell 中确认 CLI 可用：

```powershell
codex --version
```

如果 Codex 没有加入 `PATH`，将用户环境变量设置为 `codex.exe` 或 `codex.cmd` 的完整路径，然后重启小组件：

```powershell
[Environment]::SetEnvironmentVariable(
  'CODEX_USAGE_WIDGET_CODEX_PATH',
  'C:\Path\To\codex.exe',
  'User'
)
```

小组件依赖本机 Codex app server 接口；如果该接口发生变化，可能需要更新小组件。

## 安装

有 Release 时，可从本仓库的 **Releases** 页面下载 Windows x64 压缩包。解压到你选择的文件夹后，运行 `CodexUsageWidget.exe`。便携包已包含 .NET 运行时，无需单独安装运行时。

程序尚未进行 Authenticode 签名，Windows SmartScreen 可能显示“未知发布者”提示。请只从本仓库 Releases 下载，并核对 Release 附带的 SHA-256 校验值。

如果直接从 ZIP 临时目录启动，程序会复制到 `%LOCALAPPDATA%\CodexUsageWidget\app\<version>` 后重新启动。从已解压的文件夹启动时，程序会留在原目录运行。

## 如何看进度条

数字显示的是**剩余百分比**。进度条中，**蓝色表示剩余额度，灰色表示已用额度**。使用量增加时，左侧灰色区域向右扩展，右侧蓝色余量随之缩短。例如：已用 18% / 剩余 82%，显示为左侧 18% 灰色、右侧 82% 蓝色。

这只是显示方式调整。用量数据仍来自 Codex 返回的限制信息，小组件不会修改或增加账户额度。

## 使用小组件

- 拖动窗口可调整位置；小组件会保持在其他窗口上方。
- 使用快速展开按钮或**设置**切换紧凑和详情布局。
- 在**设置 → 用量**中选择 `5 小时限制`、`每周限制` 或 `剩余额度最少`，设置标题和托盘显示内容。
- 点击 `−` 按钮可将摘要移到任务栏。右键点击任务栏标签或托盘图标，可刷新、切换布局、打开设置、检查更新或退出。
- 在详情布局中展开**用量限制重置次数**可查看可用重置及到期时间。选择**使用重置**后，程序会要求确认，并消耗所选重置次数。

### 可选的运行状态点

运行状态点可显示本机是否有 Codex 任务正在运行。默认关闭；只有在你明确安装并信任钩子后才会启用。小组件不会读取提示词、回复、对话记录路径或模型输出。具体安装和移除步骤见[运行状态点](docs/ACTIVITY_DOTS.md)。

## 隐私与数据

身份验证由 Codex CLI 管理。小组件只与本机 Codex 进程通信，不抓取浏览器、不要求 API Key，也不会把会话日志发送到远程服务。没有遥测或远程用量后端。

设置和诊断日志保存在 `%LOCALAPPDATA%\CodexUsageWidget`。日志在本机保留 14 天。小组件显示 ChatGPT/Codex 订阅限制，不显示 OpenAI API 账单或 API Key 用量。Token 数量是本机活动指标，不是账单数据，也不能代替账户额度仪表。

## 从源码构建

项目通过 [`global.json`](global.json) 固定 .NET SDK 版本。在 Windows 上安装对应 SDK 后运行：

```powershell
dotnet restore .\CodexUsageWidget.slnx
dotnet build .\CodexUsageWidget.slnx -c Release --no-restore
```

发布为自包含的 Windows x64 可执行文件：

```powershell
dotnet publish .\src\CodexUsageWidget\CodexUsageWidget.csproj `
  -c Release -r win-x64 --self-contained true `
  -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true `
  -o .\artifacts\win-x64
```

维护者发布脚本是 `scripts/publish.ps1`，它会运行项目检查并创建便携压缩包。准备 Release 前请阅读[发布说明](docs/RELEASING.md)。

如需在不读取或消耗账户用量的情况下查看合成预览数据，请先关闭正在运行的小组件，再运行：

```powershell
dotnet run --project .\src\CodexUsageWidget\CodexUsageWidget.csproj `
  -p:EnableUsagePreview=true -- --preview-usage
```

## 卸载

1. 如果安装了运行状态钩子，请在**设置 → 功能 → Codex 运行状态**中移除。
2. 如果启用了**随 Windows 启动**，请在**设置 → 常规**中关闭。
3. 退出小组件并删除解压后的程序文件夹。
4. 如需清除设置和本地日志，可选删 `%LOCALAPPDATA%\CodexUsageWidget`。

## 来源与许可

本项目基于 [ognjeeen/codex-usage-widget](https://github.com/ognjeeen/codex-usage-widget) 修改，调整了剩余额度进度条：蓝色显示剩余，灰色已用量从左向右增长。原项目的 MIT 许可和版权声明保留在 [`LICENSE`](LICENSE) 中。原作者及其他贡献者的工作仍归其各自作者所有。
