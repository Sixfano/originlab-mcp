# 在 Codex 中配置 OriginLab MCP

本文说明如何在 Windows 上将 OriginLab MCP 注册到 Codex。Codex 通过用户级 `config.toml` 启动 MCP Server；不要按旧说明创建 `.codex/config.json`，也不要使用状态页中的 Codex 一键配置按钮。该按钮目前会写入不适用于 Codex 的 JSON 配置。

## 环境要求

- Windows。
- 已安装 OriginLab Origin 2021 或更高版本，并拥有有效许可。
- Python 3.10 或更高版本，以及 `uv`。
- 已将本项目克隆到本机，例如 `C:\Users\你的用户名\src\originlab-mcp`。

在项目目录打开 PowerShell，安装项目依赖：

```powershell
uv sync
```

可以先启动图形状态页并确认它能连接 Origin：

```powershell
uv run originlab-mcp-ui
```

状态页默认地址为 `http://127.0.0.1:8765/`。完成连接检查后关闭状态页；Codex 会按配置自行启动 MCP Server。若要在终端手动运行 Server，可执行：

```powershell
uv run originlab-mcp
```

## 配置 Codex

打开用户级 Codex 配置文件：

```text
%USERPROFILE%\.codex\config.toml
```

若文件或 `.codex` 目录尚不存在，请创建。将以下内容追加到文件末尾，并把示例目录替换为本机克隆项目的绝对路径：

```toml
[mcp_servers.originlab]
command = "uv"
args = ["--directory", "C:\\Users\\你的用户名\\src\\originlab-mcp", "run", "--no-sync", "originlab-mcp"]
default_tools_approval_mode = "prompt"
```

TOML 字符串中的 Windows 反斜杠要写成 `\\`。如果配置文件中已经有 `[mcp_servers.originlab]` 段落，请编辑该段落，不要重复添加；保留文件中其他 MCP Server 和 Codex 设置。

保存文件并重启 Codex。若已安装 Codex CLI，可以在 PowerShell 中检查注册结果：

```powershell
codex mcp list
```

之后在 Codex 对话中查看可用工具，应能看到 `originlab` Server 提供的 OriginLab MCP 工具。

## 排障

- **Codex 无法启动 Server：** 检查 `command = "uv"` 能否在 PowerShell 中执行 `uv --version`，并确认 `--directory` 指向含有 `pyproject.toml` 的项目目录。
- **配置改动未生效：** 确认编辑的是 `%USERPROFILE%\.codex\config.toml`，TOML 语法有效，并完全退出后重新启动 Codex。
- **MCP 工具没有出现：** 查看 Codex 的 MCP 状态或日志，确认 Server 能启动；在同一项目目录运行 `uv run originlab-mcp` 检查依赖和启动错误。
- **Server 无法连接 Origin：** 确认 Origin 已安装、有效许可可用，并且桌面会话中能够正常启动 Origin。状态页可用于检查 Origin 连接。
- **状态页中的 Codex 按钮：** 目前会写入旧式 JSON 文件，不能用于配置 Codex。请按本指南更新 TOML 文件。

更多可用字段和客户端配置说明见 [Codex MCP 官方文档](https://developers.openai.com/codex/mcp/) 与 [配置参考](https://developers.openai.com/codex/config-reference/)。

