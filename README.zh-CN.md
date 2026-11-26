# 在 Codex 里使用 Asta 查文献

[English](README.md)

给 Codex 一篇论文的 DOI，就可以查作者和摘要、追踪后续引文或定位支持某项结论的正文片段。给它一个研究问题，则可以筛选候选论文并撰写带引文的综述。这个独立的 [asta-literature Skill](asta-literature/SKILL.md) 直接调用 Ai2 托管的 Asta Scientific Corpus MCP。

## 安装 Skill

在希望存放源码的目录中运行下面对应平台的命令。命令将 Skill 复制到 Codex 用户级目录。若已安装，先检查现有版本再更新。

**Windows PowerShell**

```powershell
git clone https://github.com/Hyacehila/asta-literature-skill.git
if ($LASTEXITCODE -ne 0) { throw 'Clone failed' }
$skillsDir = Join-Path $env:USERPROFILE '.agents/skills'
$target = Join-Path $skillsDir 'asta-literature'
if (Test-Path -LiteralPath $target) { throw "Skill already installed at $target" }
New-Item -ItemType Directory -Force -Path $skillsDir | Out-Null
Copy-Item -Recurse -LiteralPath './asta-literature-skill/asta-literature' -Destination $target
```

**macOS 或 Linux**

```sh
(
  set -e
  git clone https://github.com/Hyacehila/asta-literature-skill.git
  dest="$HOME/.agents/skills/asta-literature"
  test ! -e "$dest" || { echo "Already installed at $dest"; exit 1; }
  mkdir -p "$HOME/.agents/skills"
  cp -R ./asta-literature-skill/asta-literature "$dest"
)
```

## 配置 Asta

1. 在 [Ai2 官方接入页面](https://allenai.org/asta/resources/mcp#access) 选择 **Request an Asta tool key**。具体用途和调用速率以自己的审批邮件为准。
2. 在用户配置 `~/.codex/config.toml` 中加入以下内容。Windows 路径为 `%USERPROFILE%\.codex\config.toml`。已有 `asta` 配置时直接修改对应表。

```toml
[mcp_servers.asta]
url = "https://asta-tools.allen.ai/mcp/v1"
env_http_headers = { "x-api-key" = "ASTA_TOOL_KEY" }
```

3. 在准备启动 Codex 的终端中设置密钥。Windows PowerShell 使用掩码输入：

```powershell
$secret = Read-Host 'Asta Tool Key' -AsSecureString
$env:ASTA_TOOL_KEY = [System.Net.NetworkCredential]::new('', $secret).Password
$secret.Dispose()
```

macOS 或 Linux 请在 **Bash** 中运行以下命令。使用其他 shell 时可先输入 `bash`：

```bash
read -r -s -p 'Asta Tool Key: ' ASTA_TOOL_KEY
printf '\n'
export ASTA_TOOL_KEY
```

4. 在同一终端启动 `codex` 并打开新对话。若从桌面启动，请在桌面客户端的启动环境中设置该变量，再完全退出并重开。Windows 可以通过“编辑账户的环境变量”设置用户变量。请妥善保管变量值。 [Codex MCP 配置文档](https://developers.openai.com/codex/mcp)

## 验证安装

在 Codex 中通过 `/mcp` 查看服务器状态，再发送：

> 用 $asta-literature 通过 Asta 查询 ARXIV:2510.21652，给我题名、第一作者和论文链接。

应返回 *AstaBench: Rigorous Benchmarking of AI Agents with a Scientific Research Suite*，第一作者 Jonathan Bragg，并附论文链接。 [核对原文](https://arxiv.org/abs/2510.21652)

可以追问“哪些引用它的后续工作评测了文献检索？”做综述时，可以要求 Codex 写出纳入标准，并标注哪些判断仅由摘要支持。

如果看不到服务，检查 TOML 配置并重启。遇到 401/403，检查启动进程中的环境变量及请求头名称 `x-api-key`。遇到 429，等待后按审批邮件的限额降低请求频率。查询结果为空时，核对题名或调整关键词和日期范围。

## 署名与许可

文献检索使用 Allen Institute for Artificial Intelligence（Ai2）提供的 Asta Scientific Corpus Tool。服务许可适用于学术和非商业科研。在使用服务的网站或应用上标注 Asta 来源，在相关发表物中致谢并按审批要求引用 AstaBench。Skill 改编自 Ai2 的三份官方工作流，采用 Apache-2.0 许可。 [来源与改编说明](asta-literature/NOTICES.md) · [Apache-2.0](LICENSE) · [服务许可](https://allenai.org/asta-corpus-license/2025-08-26) · [AstaBench 论文](https://arxiv.org/abs/2510.21652)
