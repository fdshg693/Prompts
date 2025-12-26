# Claudeプロンプトエンジニアリング ドキュメント作成 作業手順書

## 概要
Claude公式サイトのプロンプトエンジニアリングドキュメント（https://platform.claude.com/docs/en/build-with-claude/prompt-engineering）を`docs\claude公式`フォルダ配下に日本語でまとめる作業の手順書です。

全14個の主要ドキュメントを、1回の作業で3～4個ずつ処理していきます。

---

## 作業対象URL一覧

### 【基礎編】プロンプトエンジニアリングの基本
1. **Overview（概要）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview
   - 内容: プロンプトエンジニアリングの全体像、取り組むべき順序

2. **Be Clear and Direct（明確で直接的な指示）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/be-clear-and-direct
   - 内容: 明確で具体的な指示の書き方

3. **Use XML Tags（XML タグの活用）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/use-xml-tags
   - 内容: プロンプトの構造化にXMLタグを使う方法

4. **Multishot Prompting（例示による学習）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/multishot-prompting
   - 内容: 具体例を使ってClaudeの振る舞いを誘導する手法

---

### 【中級編】思考と構造化のテクニック
5. **Chain of Thought（思考の連鎖）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/chain-of-thought
   - 内容: ステップバイステップで考えさせることでパフォーマンスを向上

6. **System Prompts（システムプロンプト）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/system-prompts
   - 内容: ロールプロンプティングでClaudeに役割を与える

7. **Prefill Claude's Response（応答の先行入力）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prefill-claudes-response
   - 内容: 応答の最初の部分を制御する方法

8. **Chain Prompts（プロンプトの連鎖）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/chain-prompts
   - 内容: 複雑なタスクを複数のプロンプトに分割する

---

### 【応用編】特殊なシナリオとツール
9. **Long Context Tips（長文コンテキストのヒント）**
   - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/long-context-tips
   - 内容: 20万トークンの長文コンテキストを効果的に活用

10. **Extended Thinking Tips（拡張思考のヒント）**
    - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/extended-thinking-tips
    - 内容: 拡張思考モードを最大限に活用するための高度な戦略

11. **Claude 4 Best Practices（Claude 4のベストプラクティス）**
    - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices
    - 内容: Claude 4.x モデル（Sonnet 4.5、Haiku 4.5、Opus 4.5）専用のプロンプト技術

---

### 【ツール編】プロンプト生成・改善ツール
12. **Prompt Generator（プロンプト生成ツール）**
    - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompt-generator
    - 内容: 初回のプロンプトテンプレートを自動生成

13. **Prompt Improver（プロンプト改善ツール）**
    - URL: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompt-improver
    - 内容: 既存のプロンプトを自動的に最適化

---

## 作業ステップ

### 【ステップ1】基礎編ドキュメントの作成
**対象URL:** 1～4（Overview、Be Clear and Direct、Use XML Tags、Multishot Prompting）

**作成するファイル:**
- `docs\claude公式\01_プロンプトエンジニアリング概要.md`
- `docs\claude公式\02_基本テクニック.md`

**作業内容:**
1. Overview（概要）を`01_プロンプトエンジニアリング概要.md`にまとめる
   - プロンプトエンジニアリングとは
   - いつプロンプトエンジニアリングを使うべきか
   - 推奨される学習順序
   - チュートリアルへのリンク

2. 基本的な3つのテクニック（Be Clear and Direct、Use XML Tags、Multishot Prompting）を`02_基本テクニック.md`にまとめる
   - 明確で直接的な指示の書き方
   - XMLタグの活用方法
   - 例示による学習（Few-shot/Multishot）

**実行コマンド例:**
```
サブエージェントに以下のタスクを依頼:
「以下4つのURLの内容を取得して、docs\claude公式フォルダに2つのMarkdownファイルを作成してください。
1. 01_プロンプトエンジニアリング概要.md（Overview）
2. 02_基本テクニック.md（Be Clear and Direct、Use XML Tags、Multishot Prompting）

各ファイルは日本語で、具体例と実践的なアドバイスを含めてください。」
```

---

### 【ステップ2】中級編ドキュメントの作成
**対象URL:** 5～8（Chain of Thought、System Prompts、Prefill、Chain Prompts）

**作成するファイル:**
- `docs\claude公式\03_思考と構造化テクニック.md`

**作業内容:**
1. 思考の連鎖（Chain of Thought）
   - 基本的なCoT、ガイド付きCoT、構造化CoT
   - いつ使うべきか、いつ避けるべきか

2. システムプロンプト（Role Prompting）
   - systemパラメータでロールを与える
   - 精度向上、トーン調整、フォーカス改善

3. プリフィル（Prefill）
   - Assistantメッセージの先行入力
   - 出力フォーマットの制御、キャラクター維持

4. プロンプトの連鎖
   - 複雑なタスクを小さなサブタスクに分割
   - セルフコレクションループ

**実行コマンド例:**
```
サブエージェントに以下のタスクを依頼:
「以下4つのURLの内容を取得して、docs\claude公式\03_思考と構造化テクニック.mdを作成してください。
- Chain of Thought
- System Prompts
- Prefill Claude's Response
- Chain Prompts

日本語で、各技術の使い分けと実践例を詳しく説明してください。」
```

---

### 【ステップ3】応用編ドキュメントの作成
**対象URL:** 9～11（Long Context Tips、Extended Thinking Tips、Claude 4 Best Practices）

**作成するファイル:**
- `docs\claude公式\04_長文コンテキストと拡張思考.md`
- `docs\claude公式\05_Claude4ベストプラクティス.md`

**作業内容:**
1. 長文コンテキストのヒント（Long Context Tips）
   - 20万トークンの活用方法
   - 長文データの配置戦略
   - XMLタグによる構造化

2. 拡張思考のヒント（Extended Thinking Tips）
   - Extended Thinkingモードの活用
   - 思考トークンバジェットの設定
   - マルチショットプロンプティングとの併用

3. Claude 4のベストプラクティス（Claude 4 Best Practices）
   - Claude 4.x モデル固有のテクニック
   - 明示的な指示、コンテキストの提供
   - 長期的推論、状態追跡
   - フロントエンドデザイン、エージェント機能

**実行コマンド例:**
```
サブエージェントに以下のタスクを依頼:
「以下3つのURLの内容を取得して、docs\claude公式フォルダに2つのMarkdownファイルを作成してください。
1. 04_長文コンテキストと拡張思考.md（Long Context Tips、Extended Thinking Tips）
2. 05_Claude4ベストプラクティス.md（Claude 4 Best Practices）

Claude 4.x特有の機能と、最新のベストプラクティスを詳しく解説してください。」
```

---

### 【ステップ4】ツール編ドキュメントの作成
**対象URL:** 12～13（Prompt Generator、Prompt Improver）

**作成するファイル:**
- `docs\claude公式\06_プロンプト生成・改善ツール.md`

**作業内容:**
1. プロンプト生成ツール（Prompt Generator）
   - 初回ドラフトの自動生成
   - Consoleでの使い方
   - Google Colab notebookの活用

2. プロンプト改善ツール（Prompt Improver）
   - 既存プロンプトの最適化
   - 4ステップの改善プロセス
   - テストケース生成との連携

**実行コマンド例:**
```
サブエージェントに以下のタスクを依頼:
「以下2つのURLの内容を取得して、docs\claude公式\06_プロンプト生成・改善ツール.mdを作成してください。
- Prompt Generator
- Prompt Improver

各ツールの使い方、最適な活用シーン、具体例を日本語で詳しく説明してください。」
```

---

## ファイル構成の方針

### 出力先ディレクトリ
```
docs\claude公式\
  ├── 01_プロンプトエンジニアリング概要.md
  ├── 02_基本テクニック.md
  ├── 03_思考と構造化テクニック.md
  ├── 04_長文コンテキストと拡張思考.md
  ├── 05_Claude4ベストプラクティス.md
  └── 06_プロンプト生成・改善ツール.md
```

### ファイル構成の原則
1. **1ファイル = 1つの大きなテーマ** または **密接に関連する2～4つの技術**
2. **番号付けで学習順序を明示**（01から06まで）
3. **日本語で分かりやすいファイル名**
4. **各ファイルに目次、具体例、実践的アドバイスを含める**

---

## 各ファイルの記載フォーマット

各Markdownファイルは以下の構成に従ってください：

```markdown
# [タイトル]

## 概要
（このドキュメントで学べること、重要性の説明）

## 目次
- セクション1
- セクション2
- ...

---

## [セクション1のタイトル]

### 説明
（技術の詳細説明）

### なぜ使うのか
（メリット、ユースケース）

### 使い方
（具体的な手順）

### 具体例
```
（コードサンプルやプロンプト例）
```

### ポイント・注意事項
- ポイント1
- ポイント2

---

## まとめ
（重要なポイントの再確認）

## 参考リンク
- 公式ドキュメント: [URL]
- 関連チュートリアル: [URL]
```

---

## 作業上の注意点

### 内容の正確性
- 元のドキュメントの内容を正確に翻訳・要約する
- 技術的な用語は適切に日本語化（必要に応じて英語も併記）
- コード例やプロンプト例は原文のまま保持

### 読みやすさ
- 見出し、箇条書き、コードブロックを適切に使用
- 具体例を豊富に含める
- 初心者でも理解できるよう丁寧に説明

### ファイル構成
- `ai_coder\file_structure.md`に記載されているMarkdown構成規則に従う
- 各ファイルは独立して読めるようにする（他ファイルへの参照は最小限）

---

## 進捗管理

### 完了チェックリスト
- [ ] ステップ1: 基礎編（URL 1～4）→ 2ファイル作成
- [ ] ステップ2: 中級編（URL 5～8）→ 1ファイル作成
- [ ] ステップ3: 応用編（URL 9～11）→ 2ファイル作成
- [ ] ステップ4: ツール編（URL 12～13）→ 1ファイル作成

### 作業時間の目安
- 各ステップ: 1回のサブエージェント実行
- 全体: 4回の実行で完了

---

## 補足情報

### 追加で参考になるリソース
- GitHub Tutorial: https://github.com/anthropics/prompt-eng-interactive-tutorial
- Google Sheets Tutorial: https://docs.google.com/spreadsheets/d/19jzLgRruG9kjUQNKtCg1ZjdD6l6weA6qRXG5zLIAhC8
- Anthropic Cookbook: https://github.com/anthropics/anthropic-cookbook

### 関連ドキュメント（今回の対象外だが関連性が高い）
- Prompt Caching: https://platform.claude.com/docs/en/build-with-claude/prompt-caching
- Agent SDK: https://platform.claude.com/docs/en/agent-sdk/modifying-system-prompts

---

## 最後に

この作業手順書に従って、4回の作業で14個のURLを体系的にドキュメント化できます。各ステップは独立しているため、途中で中断・再開が可能です。

**次のアクション:**
まずは**ステップ1（基礎編）**から開始してください！
