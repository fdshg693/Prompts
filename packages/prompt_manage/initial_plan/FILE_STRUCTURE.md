# プロンプト管理パッケージ - FILE_STRUCTURE.md

## 1. 目的/概要

- **主目的**: Markdownベースのプロンプトテンプレートをメタデータ（YAMLフロントマター）で管理し、CLIを通じて変数代入およびマージ処理を行う
- **対象成果物**: 変数展開済みのMarkdownファイルのみを出力対象とする
- **パス解決基準**: 設定ファイル（`prompt_manage_config.yaml`）が置かれたディレクトリを基準に、すべての相対パスを解決
- **出力指定方式**: 各エントリの`output_path`で相対ディレクトリを指定し、設定ファイル位置から相対的に生成

## 2. ファイル構成

### パッケージのメインディレクトリ構成
```
prompt_manage/
├── __init__.py              # パッケージ初期化
├── cli.py                    # CLIエントリーポイント（argparseまたはclick）
├── core/                     # コアロジック
│   ├── __init__.py
│   ├── template_loader.py    # テンプレート解析・フロントマター処理
│   ├── variable_resolver.py  # 変数セット選択・展開
│   ├── merger.py             # マージテンプレート処理
│   └── extractor.py          # XMLコードブロック抽出
├── config/                   # 設定管理
│   ├── __init__.py
│   ├── models.py             # Pydanticモデル（設定スキーマ）
│   └── validator.py          # バリデーションロジック
├── exceptions.py             # カスタム例外定義
├── logger.py                 # loguruベースのロギング
└── utils/                    # ユーティリティ関数
    ├── __init__.py
    ├── path_resolver.py      # pyproject.toml検索・パス解決
    └── file_operations.py    # ファイルI/O処理
```

### テンプレートファイルの構造例

#### 単純なテンプレート（variables_sets + 抽出指定あり）
```markdown
---
variables_sets:
  - name: base_ja
    variables:
      lang: 日本語
      level: 初級
  - name: base_en
    variables:
      lang: English
      level: Beginner

extractions:
  - name: config_block
    type: xml
---

# プロンプトテンプレート

こんにちは。この文書は{{ lang }}で書かれています。

## システム設定

\`\`\`xml name="config_block"
<config>
  <language>{{ lang }}</language>
  <level>{{ level }}</level>
</config>
\`\`\`
```

#### マージ用テンプレート（複数テンプレート統合）
```markdown
---
max_templates: 2
variables_sets:
  - name: merge_ja
    variables:
      title: 統合ガイド

extractions: []
---

# {{ title }}

## パート1

{{ template_1 }}

## パート2

{{ template_2 }}
```

### 設定ファイルの構造例

#### prompt_manage_config.yaml（単一生成）
```yaml
outputs:
  - file_name: intro-ja.md
    output_path: ./outputs
    template: templates/intro.md
    variables_set: base_ja

  - file_name: system-ja.md
    output_path: ./outputs/system
    template: templates/system.md#block_1
    variables_set: sys_ja
```

#### prompt_manage_config.yaml（マージ生成）
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

### pyproject.toml での設定

```toml
[tool.prompt_manage]
templates_dir = "prompts/templates"
merge_templates_dir = "prompts/merge_templates"
```

## 3. 責務分割

### cli.py
- **責務**: CLIエントリーポイント、引数パース、メインワークフロー制御
- **主要処理**:
  - コマンドライン引数の解析（`--config`、`--templates-dir`、`--merge-templates-dir`）
  - 設定ファイルの読み込み
  - パッケージのパス解決（`pyproject.toml`検索）
  - コア処理への委譲と例外ハンドリング

### template_loader.py
- **責務**: テンプレートファイルの解析とフロントマター抽出
- **主要処理**:
  - ファイルの読み込み
  - `---`で囲まれたYAMLフロントマターの解析
  - フロントマターの妥当性チェック（必須フィールド、スキーマ準拠）
  - テンプレート本体とメタデータの分離

### variable_resolver.py
- **責務**: 指定された変数セットでの変数展開
- **主要処理**:
  - 名前付き変数セットの選択
  - テンプレート内への変数埋め込み（`{{ variable_name }}`置換）
  - 欠損変数の検出・エラー報告

### merger.py
- **責務**: マージテンプレート処理と複数テンプレートの統合
- **主要処理**:
  - マージテンプレートのフロントマター検証（`max_templates`チェック）
  - 複数テンプレートの逐次展開
  - `{{ template_n }}`プレースホルダ置換による統合
  - テンプレート数超過時のエラー検出

### extractor.py
- **責務**: XMLコードブロックの検出と抽出
- **主要処理**:
  - XMLコードフェンス（```xml name="..."）の抽出
  - 指定名とタイプ（`xml`）の一致チェック
  - 重複名チェック（同一テンプレート内での重複エラー処理）
  - 抽出内容の返却

### models.py（Pydantic）
- **責務**: 設定ファイルと入力値のスキーマ定義・バリデーション
- **主要処理**:
  - `PromptManageConfig`：設定全体スキーマ
  - `OutputEntry`：単一出力エントリスキーマ
  - `TemplateEntry`：マージ配列要素スキーマ
  - 型チェック・必須フィールド検証

### validator.py
- **責務**: 追加の業務ロジック検証（Pydantic外）
- **主要処理**:
  - テンプレートファイルの存在確認
  - パス重複・衝突チェック
  - フロントマター内の変数セット名一意性チェック
  - 抽出ターゲット名一意性チェック

### path_resolver.py
- **責務**: プロジェクトルート検出とパス解決
- **主要処理**:
  - `Path(__file__).parent`から遡ってワークスペースルート（`pyproject.toml`）を検出
  - `[tool.prompt_manage]`セクションの読み込み
  - CLIオプションによるテンプレートディレクトリ上書き
  - 相対パスの絶対パス変換

### logger.py
- **責務**: ロギング機能の統一化
- **主要処理**:
  - loguruベースのロガー初期化
  - ログレベル管理（DEBUG、INFO、ERROR など）
  - 構造化ログ出力（任意）

### exceptions.py
- **責務**: パッケージ固有の例外定義
- **主要例外**:
  - `PromptManageConfigError`：設定バリデーションエラー
  - `TemplateNotFoundError`：テンプレートファイル未検出
  - `VariableMissingError`：必須変数欠損
  - `MergeTemplateError`：マージテンプレート処理エラー
  - `ExtractionError`：XMLブロック抽出失敗
  - `PathResolutionError`：パス解決失敗

## 4. 想定ワークフロー

### 単一生成フロー
1. **CLIコマンド実行**
   - `prompt_manage generate --config ./prompt_manage_config.yaml`

2. **設定ファイル読み込み・パース**
   - YAML形式で設定を読み込み、Pydanticで検証
   - 設定ファイル置き場をベース基準に出力パスを解決

3. **パッケージルート検出**
   - `Path(__file__).parent`から遡って`pyproject.toml`を検索
   - `[tool.prompt_manage]`から`templates_dir`を取得

4. **エントリごと処理**
   - テンプレートファイルを読み込み（ファイルが見つからない場合はエラー）
   - フロントマターを解析し、指定された`variables_set`名を検証
   - 変数セットを選択し、テンプレート本体に埋め込み
   - 必要に応じてXMLコードブロックを抽出（後続処理）
   - 展開結果のMarkdownを生成

5. **出力ディレクトリ生成と書き込み**
   - 設定ファイルを基準に`output_path`を解決し、出力ディレクトリを生成
   - 指定された`file_name`でMarkdownをファイル出力
   - 処理完了ログを出力

### マージ生成フロー
1. **CLIコマンド実行と設定読み込み**
   - 単一生成フローと同様

2. **マージテンプレート読み込み・検証**
   - マージテンプレートを読み込み、フロントマターを抽出
   - `max_templates`フィールドで受け入れテンプレート数上限を確認

3. **構成テンプレート展開**
   - 設定ファイル内の各`templates`配列要素ごとに：
     - テンプレートファイルを読み込み
     - 指定された`variables_set`で変数展開
     - 展開結果を一時保存

4. **プレースホルダ置換**
   - マージテンプレート本体の`{{ template_1 }}`、`{{ template_2 }}`等を、展開結果で順次置換

5. **出力処理**
   - マージ後のMarkdownを出力ディレクトリに書き込み

## 5. パス解決と優先順位

### パス解決基準の定義

#### プロジェクトルート検出
- **実施方法**: `Path(__file__).parent`からワークスペース向け遡行し、最初に見つかった`pyproject.toml`をプロジェクトルートとする
- **対象外**: インストール済みパッケージ自身の`pyproject.toml`は探索対象外
- **警告なし**: 同一ワークスペース内複数の`pyproject.toml`やモノレポ構成でも警告しない

#### テンプレートディレクトリのデフォルト値
- **読み込み元**: `pyproject.toml`の`[tool.prompt_manage]`セクション
- **必須指定**: `templates_dir`と`merge_templates_dir`は必須
- **相対パス基準**: ワークスペースルート（`pyproject.toml`置き場）から相対

#### CLIオプションの上書き
- **オプション形式**: `--templates-dir`、`--merge-templates-dir`
- **基準ディレクトリ**: 現在の作業ディレクトリ（CWD）を基準に解釈
- **相対参照許容**: `..`によるワークスペース外参照も許容

#### 設定ファイル内パスの解決
- **テンプレートパス**: `pyproject.toml`で指定した`templates_dir`を基準に解釈（相対パス）
- **マージテンプレートパス**: `merge_templates_dir`を基準に解釈
- **絶対パス指定禁止**: 設定ファイル内では絶対パス指定を禁止

#### 出力先パスの解決
- **基準ディレクトリ**: `prompt_manage_config.yaml`が置かれたディレクトリ
- **指定方式**: 各エントリの`output_path`で相対ディレクトリを指定
- **廃止事項**: `[tool.prompt_manage].output_dir`は廃止され、使用されない
- **動的生成**: 出力ディレクトリが存在しない場合、設定ファイル位置を基準に自動作成

### パス解決の優先順位

```
優先度 1: CLIオプション（--templates-dir、--merge-templates-dir）
  ├─ 現在の作業ディレクトリ基準で解釈
  ├─ 相対パス、..による遡行も許容
  └─ 指定されない場合は優先度2へ

優先度 2: pyproject.toml の [tool.prompt_manage]
  ├─ ワークスペースルート基準の相対パス
  └─ 未定義またはファイル不在の場合はエラー

優先度 3: パッケージレベルのデフォルト
  └─ 存在しない（優先度2未満ではエラー扱い）
```

### パス解決例

#### 例1: CLI実行ディレクトリがワークスペース直下
```
構成:
  workspace/
    ├── pyproject.toml
    │   [tool.prompt_manage]
    │   templates_dir = "prompts/templates"
    └── prompt_manage_config.yaml
        output_path: ./outputs

実行コマンド:
  $ cd workspace
  $ prompt_manage generate --config prompt_manage_config.yaml

解決結果:
  - templates: workspace/prompts/templates/
  - output: workspace/outputs/
```

#### 例2: CLI実行ディレクトリが異なり、オプションで指定
```
構成:
  workspace/
    ├── pyproject.toml
    │   [tool.prompt_manage]
    │   templates_dir = "prompts/templates"
    └── config/
        └── prompt_manage_config.yaml
            output_path: ./out

実行コマンド:
  $ cd /home/user
  $ prompt_manage generate \
      --config /path/to/workspace/config/prompt_manage_config.yaml \
      --templates-dir /path/to/workspace/prompts/templates

解決結果:
  - templates: /path/to/workspace/prompts/templates/（オプション指定が優先）
  - output: /path/to/workspace/config/out/（設定ファイル基準）
```

## 6. 設定例

### 例1: 単純な単一生成

**prompt_manage_config.yaml**
```yaml
outputs:
  - file_name: system-prompt-ja.md
    output_path: ./generated
    template: system.md
    variables_set: japanese
```

**prompts/templates/system.md**
```markdown
---
variables_sets:
  - name: japanese
    variables:
      language: 日本語
      formal_level: 高い
  - name: english
    variables:
      language: English
      formal_level: High

extractions: []
---

# システムプロンプト

言語: {{ language }}
敬語度: {{ formal_level }}
```

**実行**
```bash
$ prompt_manage generate --config ./prompt_manage_config.yaml
```

**生成結果（generated/system-prompt-ja.md）**
```markdown
# システムプロンプト

言語: 日本語
敬語度: 高い
```

---

### 例2: XML抽出を含む単一生成

**prompt_manage_config.yaml**
```yaml
outputs:
  - file_name: config-extract.md
    output_path: ./outputs/config
    template: app-config.md#app_settings
    variables_set: prod_ja
```

**prompts/templates/app-config.md**
```markdown
---
variables_sets:
  - name: prod_ja
    variables:
      env: production
      region: asia-northeast1

extractions:
  - name: app_settings
    type: xml
---

# アプリケーション設定

環境: {{ env }}
リージョン: {{ region }}

\`\`\`xml name="app_settings"
<settings>
  <environment>{{ env }}</environment>
  <region>{{ region }}</region>
</settings>
\`\`\`
```

**実行**
```bash
$ prompt_manage generate --config ./prompt_manage_config.yaml
```

**生成結果（outputs/config/config-extract.md）**
```markdown
# アプリケーション設定

環境: production
リージョン: asia-northeast1

\`\`\`xml name="app_settings"
<settings>
  <environment>production</environment>
  <region>asia-northeast1</region>
</settings>
\`\`\`
```

---

### 例3: マージ生成

**prompt_manage_config.yaml**
```yaml
outputs:
  - file_name: complete-guide.md
    output_path: ./outputs
    merge_template: merge-guide.md
    templates:
      - file_name: intro.md
        variables_set: basic_ja
      - file_name: advanced.md
        variables_set: advanced_ja
```

**prompts/merge_templates/merge-guide.md**
```markdown
---
max_templates: 2
variables_sets:
  - name: default
    variables:
      title: 総合ガイド

extractions: []
---

# {{ title }}

## イントロダクション

{{ template_1 }}

## 高度な内容

{{ template_2 }}
```

**prompts/templates/intro.md**
```markdown
---
variables_sets:
  - name: basic_ja
    variables:
      lang: 日本語

extractions: []
---

基本的な説明を{{ lang }}で提供します。
```

**prompts/templates/advanced.md**
```markdown
---
variables_sets:
  - name: advanced_ja
    variables:
      topic: 高度なトピック

extractions: []
---

{{ topic }}についての詳細説明です。
```

**実行**
```bash
$ prompt_manage generate --config ./prompt_manage_config.yaml
```

**生成結果（outputs/complete-guide.md）**
```markdown
# 総合ガイド

## イントロダクション

基本的な説明を日本語で提供します。

## 高度な内容

高度なトピックについての詳細説明です。
```

---

### 例4: pyproject.toml と複数設定ファイル

**workspace/pyproject.toml**
```toml
[tool.prompt_manage]
templates_dir = "prompts/templates"
merge_templates_dir = "prompts/merge"
```

**workspace/config/prompt_manage_config.yaml**
```yaml
outputs:
  - file_name: output-1.md
    output_path: ../generated/set1
    template: basic.md
    variables_set: v1
```

**workspace/config/advanced/prompt_manage_config.yaml**
```yaml
outputs:
  - file_name: output-2.md
    output_path: ../../generated/set2
    template: advanced.md
    variables_set: v2
```

**実行**
```bash
$ cd workspace
$ prompt_manage generate --config ./config/prompt_manage_config.yaml
$ prompt_manage generate --config ./config/advanced/prompt_manage_config.yaml
```

**生成結果**
```
workspace/
├── generated/
│   ├── set1/
│   │   └── output-1.md
│   └── set2/
│       └── output-2.md
```

### 1. **Markdownテンプレート** (`templates/*.md` / `merge_templates/*.md`)

**責務:**
- プロンプト本体の構造と内容定義
- フロントマター（`---`で囲うYAML）での変数セット・抽出指定

**構成要素:**
- **フロントマター**: YAML形式（必須）
  ```yaml
  ---
  variables:
    base_ja:
      language: "日本語"
      level: "初級"
    sys_ja:
      role: "システム設計者"
  extractions:
    - name: "block_1"
      type: "xml"
    - name: "config"
      type: "xml"
  merge_max_templates: 2  # マージテンプレートのみ
  ---
  ```
- **本体**: Markdownテンプレート本文
  - 変数プレースホルダ: `{{ variable_name }}`
  - マージプレースホルダ: `{{ template_1 }}` `{{ template_2 }}` （マージテンプレートのみ）
  - コードブロック: XMLコードフェンス（抽出対象）

**制約:**
- 変数セット名はテンプレート内でユニークであること
- 抽出対象名はテンプレート内でユニークであること
- 全変数は必須で、デフォルト値なし
- XMLコードブロック以外（JSON等）は抽出対象外

---

### 2. **設定ファイル** (`prompt_manage_config.yaml`)

**責務:**
- 出力エントリ（単一生成 or マージ）の定義
- テンプレート参照と変数セット指定
- 出力ディレクトリとファイル名の指定

**配置ルール:**
- 設定ファイルが置かれたディレクトリを基準に、全ての相対パスを解決
- テンプレート/マージテンプレートのパス：設定ファイル → `pyproject.toml` で指定されたディレクトリ（相対）
- 出力ディレクトリ（`output_path`）：設定ファイル → 実際の出力先（相対）

**スキーマ: 単一生成エントリ**
```yaml
outputs:
  - file_name: "intro-ja.md"              # 出力ファイル名（厳守）
    output_path: "./outputs"              # 設定ファイルからの相対出力ディレクトリ
    template: "templates/intro.md"        # テンプレートパス（pyproject.toml の templates_dir からの相対）
    variables_set: "base_ja"              # フロントマター内の変数セット名
```

**スキーマ: マージ生成エントリ**
```yaml
outputs:
  - file_name: "merged-guide.md"
    output_path: "./outputs"
    merge_template: "templates/merge.md"  # マージテンプレートパス
    templates:
      - file_name: "templates/part_a.md"
        variables_set: "ja_a"
      - file_name: "templates/part_b.md"
        variables_set: "ja_b"
```

**特殊指定: 抽出ターゲット**
- テンプレート内の特定XMLブロックのみを使用する場合：
  ```yaml
  template: "templates/system.md#block_1"
  ```

**制約:**
- `output_path` は必須（各エントリごとに指定）
- `[tool.prompt_manage].output_dir` は廃止（使用不可）
- 全パスは相対パスのみ（絶対パス禁止）

---

### 3. **pyproject.toml**

**責務:**
- パッケージメタデータ管理
- デフォルトディレクトリ（テンプレート・マージテンプレート）指定

**セクション例:**
```toml
[tool.prompt_manage]
templates_dir = "templates"              # ワークスペースルートからの相対パス（必須）
merge_templates_dir = "merge_templates"  # ワークスペースルートからの相対パス（必須）
```

**制約:**
- `[tool.prompt_manage]` セクションは必須
- `templates_dir` / `merge_templates_dir` は必須項目
- パス指定はワークスペースルートからの相対パス
- `output_dir` は廃止（本セクションでは指定不可）

**任意設定:**
- `process_template` を直接実行するエントリポイントを `pyproject.toml` の `[project.scripts]` 等で定義すると `python -m` なしで起動可能（例: `process_template = "prompt_manage.cli:main"`）

---

### 4. **CLI**

**責務:**
- ユーザー入力の受け付けと検証
- ワークスペース検出とパス解決の制御
- テンプレート展開・マージ・出力の実行

**ワークスペース検出:**
- `pyproject.toml` を現在のスクリプト位置から遡って探索
- 最初に見つかった `pyproject.toml` を採用
- 見つからない場合 → エラー停止
- `[tool.prompt_manage]` が未定義 → エラー停止

**CLIオプション:**
```bash
python -m prompt_manage process \
  --config prompt_manage_config.yaml \
  --templates-dir custom/templates \
  --merge-templates-dir custom/merge
```

- `--config`: 設定ファイルパス **またはディレクトリ**（デフォルト: `./prompt_manage_config.yaml`）。ディレクトリ指定時は直下で `prompt_manage_config.yaml` を探索
- `--recursive-config`: 設定ファイル探索の再帰検索を有効化（デフォルト: 無効 → 指定ディレクトリ直下のみ / 有効時 → 配下を再帰し見つかった全 `prompt_manage_config.yaml` を順次実行）
- `--templates-dir`: テンプレートディレクトリ（CLIオプション > pyproject.toml）
- `--merge-templates-dir`: マージテンプレートディレクトリ（CLIオプション > pyproject.toml）

**設定ファイル探索動作:**
- `--config` に `./` や `./sub/` を渡すと、そのディレクトリ配下で `prompt_manage_config.yaml` を探索
- `--recursive-config` OFF: 指定ディレクトリ直下のみ探索し、最初に見つかった1件を実行
- `--recursive-config` ON: 指定ディレクトリ配下を再帰探索し、見つかった各設定ファイルを順番に実行
- pyproject.toml にエントリポイントを設定済みなら `process_template ...` のように `python` プレフィックスなしで実行可能

**パス解決優先順位:**
1. CLIオプション指定（現在のワーキングディレクトリを基準）
2. `pyproject.toml` 設定（ワークスペースルートを基準）

---

## 実行フロー

### 1. 準備フェーズ
```
①ワークスペース検出
  └─ pyproject.toml の [tool.prompt_manage] を読み込み
  └─ templates_dir / merge_templates_dir を確定

②設定ファイル読み込み
  └─ prompt_manage_config.yaml をパース
  └─ Pydanticで検証
```

### 2. 処理フェーズ（各エントリごと）
```
③テンプレート読み込み
  └─ フロントマター抽出と検証
  └─ 変数セット選択

④変数展開
  └─ 全変数の欠損チェック
  └─ Markdown本文へ代入

⑤抽出処理（指定時）
  └─ XMLコードブロック検出
  └─ 指定ブロックのみ抽出

⑥マージ処理（マージテンプレート指定時）
  └─ マージテンプレートのフロントマター検証
  └─ 個別テンプレート展開（①～④を再実行）
  └─ {{ template_1 }} などのプレースホルダを置換
```

### 3. 出力フェーズ
```
⑦出力ディレクトリ生成
  └─ 設定ファイル位置 + output_path で算出
  └─ 存在しない場合は作成

⑧ファイル出力
  └─ file_name をそのまま採用
  └─ 変数代入済みMarkdownを書き込み
```

---

## 責任分割の原則

| コンポーネント | 責務 | 所有者 |
|---|---|---|
| **Markdownテンプレート** | プロンプト本体 + メタデータ | ユーザー |
| **pyproject.toml** | テンプレートディレクトリ位置の統一管理 | ユーザー |
| **prompt_manage_config.yaml** | 出力エントリごとの個別設定 | ユーザー |
| **CLI** | パス解決・検証・生成実行 | パッケージ |

---

## 重要な設計ポイント

### ✓ パス解決の一貫性
- **テンプレート参照**: `pyproject.toml` → `prompt_manage_config.yaml` → パッケージコード（相対解決を段階的に実行）
- **出力先**: 設定ファイル位置 + `output_path`（常に相対）
- CLIオプション指定時のみワーキングディレクトリ基準（例外）

### ✓ 出力先の柔軟性
- **従来**: `[tool.prompt_manage].output_dir` で全出力を統一 → **廃止**
- **現在**: 各エントリが独立した `output_path` を指定 → マルチディレクトリ対応

### ✓ マージの簡潔性
- マージはプレースホルダ置換のみ（`{{ template_n }}` ）
- Python関数やフック拡張は非対応
- マージテンプレート自身も通常テンプレートと同じフロントマター構造

### ✓ エラーハンドリング
- Pydantic検証でスキーマ違反を即座に検出
- 変数欠損・XMLブロック重複など、処理中エラーは即時停止
- loguruで処理ログと例外メッセージを記録

---

## 設定ファイル例

### シンプルな単一生成
```yaml
outputs:
  - file_name: "prompt-ja.md"
    output_path: "./outputs"
    template: "templates/base.md"
    variables_set: "japanese"
```

### 複数出力
```yaml
outputs:
  - file_name: "system-ja.md"
    output_path: "./outputs/system"
    template: "templates/system.md"
    variables_set: "sys_ja"

  - file_name: "user-ja.md"
    output_path: "./outputs/user"
    template: "templates/user.md"
    variables_set: "user_ja"
```

### マージ
```yaml
outputs:
  - file_name: "full-guide.md"
    output_path: "./outputs"
    merge_template: "templates/guide.md"
    templates:
      - file_name: "templates/intro.md"
        variables_set: "intro_ja"
      - file_name: "templates/detail.md"
        variables_set: "detail_ja"
```

### 抽出指定
```yaml
outputs:
  - file_name: "config-only.md"
    output_path: "./outputs"
    template: "templates/full.md#config"
    variables_set: "base"
```
