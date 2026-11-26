# Asta Literature for Codex

[中文说明](README.zh-CN.md)

Look up a paper by DOI, trace the work that cites it, or locate the passage behind a claim. For a topic search, Codex can screen candidate studies and assemble a cited review. The installable [asta-literature](asta-literature/SKILL.md) skill uses Ai2's hosted Asta Scientific Corpus MCP directly.

## Install the skill

Run the commands below from a directory where you want the source checkout. They install a copy under your personal Codex skills directory. An existing installation is reported so you can review it before updating.

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

**macOS or Linux**

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

## Connect Asta

1. Use **Request an Asta tool key** on [Ai2's official access page](https://allenai.org/asta/resources/mcp#access). Follow the approval email for your permitted use and rate limit.
2. Add the following table to your user Codex config at `~/.codex/config.toml` (Windows: `%USERPROFILE%\.codex\config.toml`). If an `asta` table exists, update it in place.

```toml
[mcp_servers.asta]
url = "https://asta-tools.allen.ai/mcp/v1"
env_http_headers = { "x-api-key" = "ASTA_TOOL_KEY" }
```

3. Set the key in the environment that launches Codex. In Windows PowerShell use:

```powershell
$secret = Read-Host 'Asta Tool Key' -AsSecureString
$env:ASTA_TOOL_KEY = [System.Net.NetworkCredential]::new('', $secret).Password
$secret.Dispose()
```

On macOS or Linux, run the following **in Bash** (start `bash` first if necessary):

```bash
read -r -s -p 'Asta Tool Key: ' ASTA_TOOL_KEY
printf '\n'
export ASTA_TOOL_KEY
```

4. Start `codex` from that same shell and open a new chat. For a desktop client launched separately, configure the variable in its launch environment and fully restart the app. On Windows you can use **Edit environment variables for your account** to set the user variable. Keep the value private. [Codex MCP configuration](https://developers.openai.com/codex/mcp)

## Verify

Use `/mcp` to check the server. Then send:

> Use $asta-literature to look up ARXIV:2510.21652 with Asta. Give me its title, first author and source link.

Expect *AstaBench: Rigorous Benchmarking of AI Agents with a Scientific Research Suite*, first author Jonathan Bragg, with a link to the paper. [Verification record](https://arxiv.org/abs/2510.21652)

Try a follow-up such as “Which of the papers citing this one evaluate literature search?” For a review, ask Codex to explain its inclusion criteria and mark claims verified only from abstracts.

If the server is missing, check the TOML table and restart the client. For 401/403, confirm the launching process has the correct variable and the header name is `x-api-key`. For 429, wait and reduce the request rate according to your approval email. An empty query result calls for another title, keyword or date range.

## Attribution

Literature retrieval uses the Asta Scientific Corpus Tool by the Allen Institute for Artificial Intelligence. Ai2 permits academic and non-commercial research use under its service agreement. Attribute Asta in applications and acknowledge its use and cite AstaBench in relevant publications. The skill is independently adapted from three Ai2 skills under Apache-2.0. [Attribution and upstream sources](asta-literature/NOTICES.md) · [Apache-2.0](LICENSE) · [Service agreement](https://allenai.org/asta-corpus-license/2025-08-26) · [AstaBench](https://arxiv.org/abs/2510.21652)
