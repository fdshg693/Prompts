詳しい情報が取れました。GitHub Copilot CLIには2つの世代があり、**新しい方（`copilot`コマンド）**を中心に解説します。

## GitHub Copilot CLI 実用コマンドリファレンス

### 基本起動・認証

```bash
# インストール（npm経由）
npm install -g @github/copilot

# 起動（インタラクティブモード）
copilot

# バナー付きで起動
copilot --banner

# ログイン
/login

# PAT認証の場合は環境変数で
export GITHUB_TOKEN=ghp_xxxx
```

### プログラマティックモード（非インタラクティブ）

```bash
# 単発プロンプトを直接実行
copilot -p "この関数のバグを修正して"
copilot --prompt "READMEを作成して"

# ツール自動承認オプション
copilot --allow-all-tools -p "テストを実行してエラーを修正"
copilot --allow-tools=git,node -p "依存関係をインストールして"
```

### スラッシュコマンド（インタラクティブモード内）

```bash
/login              # GitHub認証
/logout             # ログアウト
/model              # 使用モデル変更（Claude Sonnet 4.5, Claude Sonnet 4, GPT-5など）
/usage              # セッションの統計（premium requests消費量、トークン使用量）
/feedback           # フィードバック送信
/clear              # 会話履歴クリア
?                   # ヘルプ表示
```

### ファイル・ディレクトリ操作

```bash
# 特定ファイルをコンテキストに含める（@記法）
Explain @config/ci/ci-required-checks.yml
Fix the bug in @src/app.js

# 信頼ディレクトリ追加
/add-dir /path/to/directory

# 作業ディレクトリ変更（セッション維持したまま）
/cwd /path/to/directory
```

### シェルコマンド直接実行

```bash
# ! プレフィックスでモデル呼び出しなしにシェル実行
!git status
!ls -la
!npm install
```

### タスク委譲（Copilot Coding Agent連携）

```bash
# ローカルセッションをGitHub上のCopilot coding agentへ移譲
/delegate complete the API integration tests and fix any failing edge cases

# 動作：
# - unstaged changesをコミット
# - 新ブランチ作成
# - draft PR作成
# - バックグラウンドで作業継続
```

### セッション管理

```bash
# 前回セッションから再開
copilot --resume

# 直前のセッションを即座に継続
copilot --continue
```

### MCP（Model Context Protocol）サーバー管理

```bash
# MCPサーバー追加（インタラクティブ入力）
/mcp add

# MCPサーバー一覧
/mcp list

# 設定ファイル: ~/.copilot/mcp-config.json
```

### カスタムエージェント使用

```bash
# エージェント一覧から選択
/agent

# 特定エージェント指定でプロンプト実行
copilot --agent=refactor-agent --prompt "このコードをリファクタリングして"

# プロンプト内で暗黙的に呼び出し
Use the refactoring agent to refactor this code block
```

### ヘルプ・設定確認

```bash
copilot help                # 全体ヘルプ
copilot help config         # 設定項目の説明
copilot help environment    # 環境変数一覧
copilot help logging        # ログレベル設定
copilot help permissions    # ツール許可/拒否設定
```

### ツール承認オプション（プログラマティック用）

| オプション | 説明 |
|-----------|------|
| `--allow-all-tools` | 全ツールを自動承認（危険） |
| `--allow-tools=TOOL1,TOOL2` | 特定ツールのみ承認 |
| なし | 都度承認（デフォルト・安全） |

---

### 旧CLI（gh copilot）について

旧来の `gh copilot` 拡張は廃止され、新しい GitHub Copilot CLI に置き換えられました。参考までに旧コマンド：

```bash
# 旧：gh CLI拡張版（deprecated）
gh copilot suggest -t shell "ファイル検索"
gh copilot explain "traceroute github.com"
```

現在は `npm install -g @github/copilot` でインストールする新CLIを使ってください。

十分な情報が得られました。まとめます。

---

## GitHub Copilot CLI デフォルトツール一覧と解説

最もまとまった情報源は **[DeepWiki - Tool Execution & Permissions](https://deepwiki.com/github/copilot-cli/3.5-tool-execution-and-permissions)** です。これはcopilot-cliリポジトリのchangelogを解析して自動生成されたドキュメントで、非常に詳細です。

---

### ツールの3カテゴリ

| カテゴリ | 説明 | 権限スコープ |
|---------|------|-------------|
| **Shell Commands** | ローカルシェル（bash/PowerShell）でコマンド実行 | パスベース（読み書きディレクトリ） |
| **GitHub MCP Tools** | GitHub APIとの連携（MCPプロトコル経由） | GitHub APIトークン権限 |
| **Custom Agents** | ユーザー定義エージェント | シェル＋MCP複合権限 |

---

### 組み込みシェルツール

実際のツールリスト（Issue #556より）：

- `powershell` / `bash` - シェルコマンド実行
- `write_powershell` / `read_powershell` / `stop_powershell` - PowerShell制御
- `view` - ファイル閲覧
- `create` - ファイル作成
- `edit` - ファイル編集
- `report_intent` - 意図報告

---

### デフォルトGitHub MCPツール（v0.0.350以降）

コンテキストウィンドウ節約のため、デフォルトでは限定セットのみ有効：

| カテゴリ | ツール名 |
|---------|---------|
| **コード・リポジトリ** | `get_file_contents`, `search_code`, `search_repositories`, `list_branches`, `list_commits`, `get_commit` |
| **Issue管理** | `get_issue`, `list_issues`, `get_issue_comments`, `search_issues` |
| **PR管理** | `pull_request_read`, `list_pull_requests`, `search_pull_requests` |
| **Workflow** | `list_workflows`, `list_workflow_runs`, `get_workflow_run`, `get_job_logs`, `get_workflow_run_logs` |
| **検索** | `search_users`, `web_search` |

全MCPツールを有効化するには：
```bash
copilot --enable-all-github-mcp-tools
```

---

### パス権限システム

デフォルトで承認済みのパス：
- **一時ディレクトリ**（システムのtempディレクトリ）
- **現在の作業ディレクトリ**（`/cwd`で変更可能）
- **`/dev/null`**（出力リダイレクト常に許可）
- **GitHub Actions Workspace**（`GITHUB_WORKSPACE`環境変数）

---

### ツール許可/拒否のGlobパターン

```bash
# 特定パターンを許可
copilot --allow-tool "shell(npm run test:*)"
copilot --allow-tool "github:search_*"

# 特定パターンを拒否（denyはallowより優先）
copilot --deny-tool "shell(rm *)"
copilot --deny-tool "shell(sudo *)"

# 全パス許可（CI/CD環境向け）
copilot --allow-all-paths

# 全ツール許可（危険）
copilot --allow-all-tools
```

---

### サブコマンドホワイトリスト対応ツール

以下のコマンドはサブコマンド単位で承認可能：
- `npm`, `yarn`, `pnpm`
- `cargo`, `go`, `dotnet`
- `mvn`, `gradle`, `gradlew`

例：`npm run test`を承認すると、そのサブコマンドのみ許可される。

---

### 読み取り専用として自動承認されるコマンド

標準的なbash/PowerShellの読み取り専用コマンドは書き込み権限チェックをバイパス。また：
- シェルリダイレクト（`> file.txt`が承認済みパス内、`> /dev/null`、`2>&1`）
- `gh api /repos/user/repo`のような引数はファイルパスとして扱われない

---

### 並列ツール実行

```bash
# 無効化オプション
copilot --disable-parallel-tools-execution
```

LLMが複数ツールを並列リクエストした場合、全ツールの事前承認後に同時実行される。

---

### 参考リンク

- [DeepWiki - Tool Execution & Permissions](https://deepwiki.com/github/copilot-cli/3.5-tool-execution-and-permissions) - 最も詳細
- [GitHub Docs - About Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli) - 公式
- [GitHub Blog - Copilot CLI 101](https://github.blog/ai-and-ml/github-copilot-cli-101-how-to-use-github-copilot-from-the-command-line/) - ユースケース集
