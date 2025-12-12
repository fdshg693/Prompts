# FILE_STRUCTURE_OVERVIEW

## 目的/概要
- テンプレート、設定、出力の関係をコンパクトに整理し、運用フローを共有する
- 各エントリの`output_path`は必ず`prompt_manage_config.yaml`が置かれたディレクトリからの相対パスで指定

## ファイル構成
- `prompt_manage_config.yaml`（出力先ディレクトリ直下）: `output_path` / `file_name` / `template`または`merge_template` / `variables_set` などを定義
- `templates/`（`pyproject.toml`の`[tool.prompt_manage].templates_dir`）: 単体展開用テンプレート
- `merge_templates/`（`[tool.prompt_manage].merge_templates_dir`）: マージ用テンプレート（`{{ template_n }}`プレースホルダ）
- 出力ディレクトリ: 各エントリの`output_path`を設定ファイル基準で解決し、存在しなければ自動生成。`file_name`でMarkdownを保存
- `pyproject.toml`: `templates_dir` / `merge_templates_dir` をワークスペースルートからの相対パスで必須定義
- CLIオプション: `templates_dir` / `merge_templates_dir` の上書き

## 責務分割
- CLI: 設定読込、パス解決、レンダリング/マージ、ロギング、例外処理
- バリデーション: Pydanticで`prompt_manage_config.yaml`を検証（絶対パス禁止、必須項目チェック）
- レンダリング: 指定`variables_set`で単一展開、欠損はエラー
- 抽出: フロントマター`extractions`で宣言された属性付きXMLコードフェンスのみ抽出
- マージ: マージテンプレート内`{{ template_n }}`の単純置換のみ

## 想定ワークフロー
1. フロントマター付きテンプレートを用意し、変数セットと抽出対象を定義
2. `pyproject.toml`に`templates_dir`/`merge_templates_dir`を設定
3. 出力先ディレクトリで`prompt_manage_config.yaml`を作成し、`output_path`（設定ファイル基準の相対）と各エントリを記述
4. 必要ならマージテンプレートと`templates`配列を定義
5. CLIを実行（パス解決→バリデーション→展開→抽出→マージ→出力）
6. 生成Markdownを`output_path`配下に`file_name`で保存

## パス解決と優先順位
- 優先度: CLIオプション > `pyproject.toml` (`[tool.prompt_manage]`) > パッケージデフォルトなし
- テンプレート/マージテンプレート: `templates_dir` / `merge_templates_dir`基準の相対パスのみ許可（絶対パス不可）
- 出力: `output_path`を設定ファイル設置ディレクトリ基準で解決
- `pyproject.toml`不在または`[tool.prompt_manage]`未定義はエラー
- CLIオプションのパスはCWD基準で解釈し、`..`も可

## 設定例
- マージなし
```yaml
outputs:
  - file_name: intro-ja.md
    output_path: ./outputs
    template: templates/intro.md
    variables_set: base_ja
```

- マージあり
```yaml
outputs:
  - file_name: merged-guide.md
    output_path: ./outputs
    merge_template: templates/merge.md
    templates:
      - file_name: templates/part_a.md
        variables_set: ja_a
      - file_name: templates/part_b.md
        variables_set: ja_b
```