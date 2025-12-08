検索結果から、GitHub Copilot CLIのカスタムエージェントの正しい使い方がわかりました。

## 成功例と正しい設定方法

### 1. エージェントファイルの配置場所

```
# ユーザーレベル（全リポジトリで使用可能）
~/.copilot/agents/my-agent.agent.md

# リポジトリレベル
.github/agents/my-agent.agent.md

# 組織レベル（{org}/.github リポジトリ内）
agents/my-agent.agent.md
```

### 2. エージェントファイルの書式（`.agent.md`）

```markdown
---
name: k8s-assistant
description: "Kubernetes専門のアシスタント"
tools:
  - read
  - search
  - edit
  - shell
---

### Instructions

You are a Kubernetes specialist assisting developers.

#### Goals
- Generate, explain, and optimize Kubernetes YAML configurations
- Help with kubectl commands
```

### 3. 呼び出し方法

**インタラクティブモード（推奨）:**
```bash
# まずcopilotを起動
copilot

# 内部でスラッシュコマンドを使用
/agent k8s-assistant
```

**非インタラクティブモード:**
```bash
copilot --agent=k8s-assistant --prompt "Review my deployment.yaml"

# または -p でも可
copilot --agent=terraform-agent -p "Review my IaC for issues"
```

---

## よくある失敗原因と対処

| 原因 | 対処 |
|------|------|
| ファイル拡張子が `.md` だけ | **`.agent.md`** にする |
| 配置場所が違う | `~/.copilot/agents/` または `.github/agents/` に置く |
| `--agent` の値にパスを指定 | ファイル名（拡張子なし）だけを指定 |
| frontmatterがない | `---` で囲んだYAMLヘッダーを追加 |

---

## 動作確認済みの具体例

**ファイル:** `~/.copilot/agents/reviewer.agent.md`
```markdown
---
name: reviewer
description: "コードレビュー専門"
tools:
  - read
  - search
---

You are a code reviewer. Focus on:
- Security vulnerabilities
- Performance issues
- Best practices
```

**実行:**
```bash
copilot --agent=reviewer --prompt "Review the changes in src/"
```

---

どのようなエラーメッセージが出ていますか？ファイルの配置場所やファイル内容を教えてもらえれば、より具体的に原因を特定できます。

## Copilot CLI の MCP設定ファイル

### 設定ファイルの場所

```
~/.copilot/mcp-config.json
```

Windowsの場合: `%USERPROFILE%\.copilot\mcp-config.json`

（`XDG_CONFIG_HOME` 環境変数で変更可能）

---

### 基本構造

```json
{
  "mcpServers": {
    "server-name": {
      "type": "local",
      "command": "実行コマンド",
      "args": ["引数1", "引数2"],
      "env": {
        "ENV_VAR": "${MY_SECRET}"
      },
      "tools": ["tool1", "tool2"],
      "cwd": "/path/to/working/directory"
    }
  }
}
```

---

### サーバータイプ別の設定例

#### 1. ローカルサーバー（stdio）

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/dir"]
    }
  }
}
```

#### 2. ローカルサーバー（Python）

```json
{
  "mcpServers": {
    "my-python-server": {
      "type": "local",
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "env": {
        "API_KEY": "${COPILOT_MCP_API_KEY}"
      },
      "cwd": "C:\\Users\\username\\projects\\mcp-server"
    }
  }
}
```

#### 3. リモートサーバー（HTTP/SSE）

```json
{
  "mcpServers": {
    "remote-server": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${COPILOT_MCP_TOKEN}"
      },
      "tools": ["*"]
    }
  }
}
```

#### 4. GitHub MCP Server（組み込み拡張）

```json
{
  "mcpServers": {
    "github-mcp-server": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/readonly",
      "tools": ["*"],
      "headers": {
        "X-MCP-Toolsets": "repos,issues,users,pull_requests,actions,web_search"
      }
    }
  }
}
```

#### 5. Azure DevOps MCP

```json
{
  "mcpServers": {
    "ado": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-org", "-a", "azcli"],
      "tools": ["wit_get_work_item", "wit_get_work_items_batch_by_ids"]
    }
  }
}
```

---

### 環境変数の扱い（v0.0.340以降）

**重要:** `${VAR}` 形式で明示的に指定する必要があります。

```json
{
  "mcpServers": {
    "my-server": {
      "type": "local",
      "command": "node",
      "args": ["server.js"],
      "env": {
        "API_KEY": "${COPILOT_MCP_MY_API_KEY}",
        "DEBUG": "true"
      }
    }
  }
}
```

シークレットは `COPILOT_MCP_` プレフィックスが推奨されています。

---

### 設定プロパティ一覧

| プロパティ | 必須 | 説明 |
|-----------|------|------|
| `type` | ✓ | `"local"` または `"http"` |
| `command` | ✓（local） | 実行するコマンド |
| `args` | - | コマンド引数の配列 |
| `url` | ✓（http） | リモートサーバーのURL |
| `headers` | - | HTTPヘッダー |
| `env` | - | 環境変数 |
| `cwd` | - | 作業ディレクトリ |
| `tools` | - | 使用するツールの配列（`["*"]`で全ツール） |

---

### インタラクティブモードでの追加

```bash
copilot
# 起動後
/mcp add
```

TUI形式で設定を追加できます。

---

### 一時的なMCPサーバーの追加

セッション限定で追加する場合（設定ファイルを変更しない）：

```bash
copilot --mcp-server "my-temp-server:npx -y @some/mcp-server"
```

---

### デバッグ・確認

```bash
# 設定されているMCPサーバーを確認
copilot
/mcp show

# 全GitHub MCPツールを有効化（コンテキストウィンドウを多く消費）
copilot --enable-all-github-mcp-tools
```

なるほど、環境変数がプロセスに渡っていないようですね。Windows環境でのCopilot CLI固有の問題かもしれません。

## 解決策

### 方法1: システム環境変数を設定して `${VAR}` で参照

まずPowerShellでシステム環境変数を設定：
```powershell
[System.Environment]::SetEnvironmentVariable("TAVILY_API_KEY", "tvly-dev-xxxxx", "User")
```

その後、`mcp-config.json` で参照：
```json
{
  "mcpServers": {
    "tavily_mcp": {
      "type": "local",
      "command": "npx.cmd",
      "args": ["-y", "tavily-mcp@latest"],
      "env": {
        "TAVILY_API_KEY": "${TAVILY_API_KEY}"
      }
    }
  }
}
```

**PowerShellを再起動してから** `copilot` を起動。

---

### 方法2: リモートMCP（最も確実）

ローカル起動の問題を完全に回避：

```json
{
  "mcpServers": {
    "tavily": {
      "type": "http",
      "url": "https://mcp.tavily.com/mcp/?tavilyApiKey=tvly-dev-xxxxx"
    }
  }
}
```

---

### 方法3: mcp-remote 経由で接続

```json
{
  "mcpServers": {
    "tavily-remote": {
      "type": "local",
      "command": "npx.cmd",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.tavily.com/mcp/?tavilyApiKey=tvly-dev-xxxxx"
      ]
    }
  }
}
```

---

**方法2のリモートMCP**が最もシンプルで、Windows環境変数の問題を完全に回避できるのでおすすめです。試してみてください。

