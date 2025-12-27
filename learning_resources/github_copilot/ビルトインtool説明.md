# Github Copilotのツール一覧(ビルトインのもののみ)
Github Copilotで使用可能なビルトインツールの一覧とその説明です。
一部ツールはユーザ側では選択できるものの、AIには何もツールが渡らない場合もあるようです。
また、ファイル編集に使われる`edit/editFiles`ツールは非常に特別なツールであり、2つの関数を同時に提供しています。
（apply_patchとinsert_edit_into_fileを提供している。これは、ファイル編集という複雑なツールの性質上AIが正しく呼び出せない可能性もあるため、通常用途のapply_patchに加えて、より成功しやすいinsert_edit_into_fileも提供しているためです。）

ユーザに見えているツール名と、実際にAIに渡されているツール名は必ずしも一致しません。
以下では、tool_name(`namespace/toolName`)の形式で記載しており、tool_nameがAIに渡されている実際のツール名で、括弧内がユーザに見えているツール名です。
プロンプト等でツールを指定する場合は`namespace/toolName`の形式で指定してください。

## ファイル編集・作成系

※`edit/editFiles`ツールは2つの関数を同時に提供します。（apply_patchとinsert_edit_into_file）
### apply_patch(`edit/editFiles`)
- テキストファイルの編集用ツール
- V4A diff形式を使用して差分を適用
- Add、Update、Delete操作が可能
- Jupyter Notebookには使用不可

### insert_edit_into_file(`edit/editFiles`)
- 既存ファイルに新しいコードを挿入
- ファイルごとに1回の呼び出しで複数変更に対応
- 簡潔なコメント形式で既存コードを表現
- スマートなコード理解により最小限のヒントで編集可能

### create_file(`edit/createFile`)
- 新規ファイルを作成
- 指定されたコンテンツでファイルを作成
- ディレクトリが存在しない場合は自動作成
- 既存ファイルの編集には使用不可

### create_directory(`edit/createDirectory`)
- 新しいディレクトリ構造を作成
- `mkdir -p`のように再帰的にディレクトリを作成
- create_fileを使う前にこのツールを使用する必要はない

## Notebook系

### create_new_jupyter_notebook(`edit/createJupyterNotebook`)
- 新しいJupyter Notebook (.ipynb)を生成
- データ探索、分析、可視化に使用
- 明示的に要求された場合のみ使用を推奨

### edit_notebook_file(`edit/editNotebook`)
- 既存のNotebookファイルを編集
- セルの挿入、削除、編集操作をサポート
- Markdownセルとコードセルの両方に対応

### copilot_getNotebookSummary(`read/getNotebookSummary`)
- Notebookセルの一覧を取得
- セルID、タイプ、行範囲、言語、実行情報を返す
- 実行順序や出力の有無を確認可能

### run_notebook_cell(`execute/runNotebookCell`)
- Notebookエディタでコードセルを直接実行
- 実行結果を返す
- Markdownセルは実行不可

## 検索・探索系

### file_search(`search/fileSearch`)
- Globパターンでファイルを検索
- ファイルパスのみを返す
- 正確なファイル名パターンが分かっている場合に使用
- 例: `**/*.{js,ts}`, `src/**`, `**/foo/**/*.js`

### grep_search(`search/textSearch`)
- ワークスペース内の高速テキスト検索
- 正確な文字列または正規表現で検索
- 特定ファイル内の検索も可能
- alternation (|) や文字クラスで複数キーワードを一度に検索可能
- ファイルの概要把握に便利

### semantic_search(`search/codebase`)
- 自然言語による意味検索
- コードやドキュメントコメントから関連する部分を検索
- ワークスペースが大きい場合は関連スニペットを、小さい場合は全体を返す
- 関数名、変数名、コメントなど、コードベースに現れそうなテキストで検索

### list_code_usages(`search/usages`)
- 関数、クラス、メソッド、変数などの全使用箇所をリスト
- 参照、定義、実装を取得
- インターフェースやクラスの実装例を探す場合に便利
- 関数の使用状況の確認や、変更時の影響範囲把握に使用

## ファイル操作系

### read_file(`read/readFile`)
- ファイルの内容を読み取る
- 行範囲を指定する必要あり（1始まり）
- 複数回の小さな読み取りより、大きな範囲を一度に読む方が推奨

### list_dir(`search/listDirectory`)
- ディレクトリの内容を一覧表示
- 名前が`/`で終わる場合はフォルダ、それ以外はファイル

## Git関連

### get_changed_files(`search/changes`)
- Gitリポジトリの現在のファイル変更の差分を取得
- staged、unstaged、merge-conflictsの状態でフィルタリング可能

## エラー・問題系

### get_errors(`read/problems`)
- コンパイルエラーやlintエラーを取得
- 特定ファイルまたは全ファイルを対象
- ファイル編集後の検証にも使用
- ユーザーが見ているエラーと同じものを確認可能

### test_failure(`execute/testFailure`)
- テスト失敗情報をプロンプトに含める

## Web関連

### fetch_webpage(`web/fetch`)
- Webページからメインコンテンツを取得
- Webページの要約や分析に便利
- 特定のWebページから情報を探す際に使用

### open_simple_browser(`vscode/openSimpleBrowser`)
- エディタ内のSimple BrowserでWebサイトをプレビュー
- ローカルホストのWebサイトやデモの表示に便利
- http/httpsのURLのみ対応

## ワークスペース・プロジェクト系

### create_new_workspace(`vscode/newWorkspace`)
- プロジェクト構造の包括的なセットアップ手順を取得
- フレームワーク全体の初期化とスキャフォールディング用
- TypeScriptプロジェクト、Reactアプリ、Node.jsサーバーなど
- MCPサーバー、VS Code拡張機能、Next.js、Viteなどのプロジェクトに対応
- 単一ファイルの作成や既存プロジェクトへの追加には使用しない

### get_project_setup_info(`vscode/getProjectSetupInfo`)
- プロジェクトタイプと言語に基づくセットアップ情報を提供
- create_new_workspaceの呼び出し後にのみ使用
- 対応タイプ: python-script、python-project、mcp-server、vscode-extension、next-js、viteなど

## VS Code関連

### get_vscode_api(`vscode/vscodeAPI`)
- VS Code API の包括的なドキュメントとリファレンスを取得
- 拡張機能開発のための公式ドキュメント
- Proposed API、contribution points、ベストプラクティスを提供
- 拡張機能開発専用（一般的なファイル作成には使用しない）

### install_extension(`vscode/installExtension`)
- VS Codeに拡張機能をインストール
- 新しいワークスペース作成プロセスの一部としてのみ使用
- `<publisher>.<extension>`形式のIDを指定

### run_vscode_command(`vscode/runCommand`)
- VS Codeコマンドを実行
- 新しいワークスペース作成プロセスの一部としてのみ使用

### vscode_searchExtensions_internal(`vscode/extensions`)
- VS Code Extensions Marketplaceを検索
- カテゴリ、キーワード、拡張機能IDで検索可能
- 拡張機能の詳細情報を取得
- カテゴリ: AI、Azure、Chat、Data Science、Debuggers、Formatters、Linters、Themesなど

## タスク・ターミナル系

### create_and_run_task(`execute/createAndRunTask`)
- ワークスペースのビルド、実行、カスタムタスクを作成・実行
- tasks.jsonファイルを生成または追加
- プロジェクト構造（package.json、README.mdなど）に基づく
- shellタイプのタスクをサポート

### run_in_terminal(`execute/runInTerminal`)
- Windows PowerShell 5.1コマンドを永続的なターミナルセッションで実行
- 環境変数、作業ディレクトリ、コンテキストを保持
- セミコロン`;`でコマンドをチェーン（`&&`は使用不可）
- バックグラウンドプロセスのサポート
- 出力は60KBで自動切り捨て

### get_terminal_output(`execute/getTerminalOutput`)
- run_in_terminalで開始したターミナルコマンドの出力を取得
- ターミナルIDを指定

### terminal_last_command(`read/terminalLastCommand`)
- アクティブなターミナルで最後に実行されたコマンドを取得

### terminal_selection(`read/terminalSelection`)
- アクティブなターミナルの現在の選択範囲を取得

## サブエージェント系

### runSubagent(`agent/runSubagent`)
- 複雑な複数ステップのタスクを自律的に処理するエージェントを起動
- 複雑な質問の調査、コード検索、複数ステップタスクの実行に優れる
- エージェントは同期的に実行（バックグラウンドではない）
- ステートレス（追加メッセージの送信不可）
- 詳細なタスク記述と期待する返却情報を指定

## その他

### get_search_view_results(`search/searchResults`)
- 検索ビューからの結果を取得

### manage_todo_list(`todo`)
- 構造化されたTodoリストを管理
- 複雑な複数ステップの作業の計画と追跡
- タスクの状態管理: not-started、in-progress、completed
- 進捗の可視化とチェックポイント管理に使用

### UI上選択できるたけで、AIには渡っていないと考えられるツール
- `execute/runTask`
- `execute/runTests`
- `execute/getTaskOutput`
- `read/readNotebookCellOutput`
- `web/githubRepo`