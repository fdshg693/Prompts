# Claude 4ベストプラクティス

## 概要

このドキュメントでは、Claude 4.xモデル（Sonnet 4.5、Haiku 4.5、Opus 4.5）に特化したプロンプトエンジニアリング技術を学びます。Claude 4.xモデルは、以前の世代よりも正確な指示追従能力を持つよう訓練されており、これらの新機能を最大限に活用するための実践的なガイドラインを提供します。

**学べる主要トピック:**
- 明示的な指示とコンテキスト提供の重要性
- 長期的推論と状態追跡の活用方法
- 並列ツール実行の最適化
- フロントエンドデザインとエージェント機能
- Claude 4.x特有の振る舞いと調整方法

## 目次

- 一般原則
  - 明示的な指示を与える
  - コンテキストを追加してパフォーマンスを向上
  - 例と詳細に注意を払う
- 長期的推論と状態追跡
  - コンテキスト認識とマルチウィンドウワークフロー
  - 複数コンテキストウィンドウのワークフロー
  - 状態管理のベストプラクティス
- コミュニケーションスタイル
- 特定状況への対応
  - 冗長性のバランス調整
  - ツール使用パターン
  - 応答フォーマットの制御
  - リサーチと情報収集
  - サブエージェントオーケストレーション
  - モデル自己認識
  - 思考の感度
  - 思考機能の活用
  - ドキュメント作成
  - 改善されたビジョン機能
  - 並列ツール呼び出しの最適化
  - エージェントコーディングでのファイル作成削減
  - 過度な積極性とファイル作成
  - フロントエンドデザイン
  - テスト合格とハードコーディングに集中しすぎない
  - コード探索の促進
  - エージェントコーディングでの幻覚の最小化
- マイグレーションの考慮事項

---

## 一般原則

### 明示的な指示を与える

#### 概要
Claude 4.xモデルは、明確で明示的な指示に対して非常に良好に反応します。望む出力について具体的に指示することで、結果を大幅に向上させることができます。

#### なぜ使うのか
以前のClaudeモデルの「期待以上の動作」を望む場合、新しいモデルではこれらの動作をより明示的にリクエストする必要があります。Claude 4.xは正確な指示追従に優れているため、あいまいさを排除することが重要です。

#### 使い方
望む動作を明確かつ具体的に記述します。「これをやってほしい」という期待を暗黙の了解にせず、直接的に伝えましょう。

#### 具体例

**悪い例:**
```
この顧客データを分析してください。
```

**良い例:**
```
この顧客データを分析し、以下を含めてください：
1. 主要なトレンドと購買パターンの特定
2. 季節性の影響分析
3. 顧客セグメント別の詳細な内訳
4. 今後6ヶ月間の予測
5. 実行可能な推奨事項

各セクションには具体的なデータポイントとビジュアル化の提案を含めてください。
```

#### ポイント・注意事項
- Claude 4.xは「察する」よりも「明示的に伝えられた」指示に優れている
- 期待する動作をすべて列挙することを恐れない
- 詳細すぎることによる問題よりも、あいまいさによる問題の方が大きい

---

### コンテキストを追加してパフォーマンスを向上

#### 概要
指示の背後にあるコンテキストや動機を提供することで、Claude 4.xモデルはあなたの目標をより深く理解し、より的確な応答を提供できます。

#### なぜ使うのか
Claudeは十分に賢いため、説明から一般化できます。「なぜこの行動が重要なのか」を理解することで、より適切な判断と出力が可能になります。

#### 使い方
タスクの目的、重要性、使用される状況について追加の説明を含めます。

#### 具体例

**基本的な指示:**
```
このコードをリファクタリングしてください。
```

**コンテキスト付き指示:**
```
このコードをリファクタリングしてください。このコードベースは複数のジュニア開発者によってメンテナンスされるため、可読性と保守性が最優先です。また、このモジュールは高トラフィックのAPIエンドポイントで使用されるため、パフォーマンスの最適化も重要です。
```

#### ポイント・注意事項
- 「何を」だけでなく「なぜ」も伝える
- ターゲットオーディエンスや使用状況を説明する
- 優先順位や制約条件を明確にする

---

### 例と詳細に注意を払う

#### 概要
Claude 4.xモデルは、正確な指示追従能力の一環として、詳細や例に細心の注意を払います。提供する例が、期待する動作と一致していることを確認する必要があります。

#### なぜ使うのか
Claude 4.xは例から学習する能力が高いため、不適切な例や矛盾する例が含まれていると、望まない動作を学習してしまう可能性があります。

#### 使い方
- 提供する例が、奨励したい動作と一致していることを確認
- 避けたい動作を示す例は最小限にする
- すべての例が同じ品質基準を満たしていることを確認

#### 具体例

**悪い例（矛盾する例）:**
```
以下のようにフォーマットしてください：

例1: 簡潔で明確な説明
例2: これは非常に長く詳細で冗長な説明で、多くの不必要な情報を含んでいます...
```

**良い例（一貫した例）:**
```
以下のようにフォーマットしてください：

例1: 簡潔で明確な要約（1-2文）
例2: 的確で焦点を絞った説明（1-2文）
例3: 要点を押さえた簡潔な記述（1-2文）
```

#### ポイント・注意事項
- すべての例が同じパターンと品質レベルを示すように注意
- 例の数を増やすよりも、質の高い一貫した例を提供する方が効果的
- 反例（「こうしないでください」）を使う場合は慎重に

---

## 長期的推論と状態追跡

### コンテキスト認識とマルチウィンドウワークフロー

#### 概要
Claude 4.5モデルは、優れた状態追跡能力を備えた長期的推論タスクに秀でています。一度に少数のことに焦点を当て、着実に前進することで、拡張セッション全体にわたって方向性を維持します。

#### なぜ使うのか
Claude 4.5モデルは「コンテキスト認識」機能を備えており、会話を通じて残りのコンテキストウィンドウ（「トークン予算」）を追跡できます。これにより、Claudeはコンテキストをより効果的に管理し、タスクを実行できます。

#### 使い方

**コンテキスト制限の管理**

エージェントハーネス内でClaudeを使用している場合、コンテキストが圧縮されたり外部ファイルに保存される仕組みがあるなら、その情報をプロンプトに追加することを推奨します。

#### 具体例

```
Your context window will be automatically compacted as it approaches its limit, allowing you to continue working indefinitely from where you left off. Therefore, do not stop tasks early due to token budget concerns. As you approach your token budget limit, save your current progress and state to memory before the context window refreshes. Always be as persistent and autonomous as possible and complete tasks fully, even if the end of your budget is approaching. Never artificially stop any task early regardless of the context remaining.
```

#### ポイント・注意事項
- メモリツールとコンテキスト認識を組み合わせることで、シームレスなコンテキスト遷移が可能
- トークン予算を理由に作業を早期に終了させないよう明示的に指示する
- コンテキストウィンドウがリフレッシュされる前に、進捗状況を保存するよう促す

---

### 複数コンテキストウィンドウのワークフロー

#### 概要
複数のコンテキストウィンドウにまたがるタスクに対する戦略的なアプローチ方法です。

#### なぜ使うのか
長期的なタスクでは、単一のコンテキストウィンドウに収まらない場合があります。適切な戦略を使用することで、複数のウィンドウにまたがっても一貫性と品質を維持できます。

#### 使い方

**1. 最初のコンテキストウィンドウには異なるプロンプトを使用**
- 最初のウィンドウでフレームワークを設定（テストの作成、セットアップスクリプト）
- 将来のコンテキストウィンドウでTODOリストを反復処理

**2. 構造化されたフォーマットでテストを作成させる**
- 作業を開始する前にテストを作成するようClaudeに依頼
- 構造化されたフォーマット（例：`tests.json`）で追跡
- テストの重要性を強調：「テストの削除や編集は、機能の欠落やバグにつながる可能性があるため許容できません」

**3. 生活の質を向上させるツールを設定**
- セットアップスクリプト（例：`init.sh`）の作成を推奨
- サーバーの起動、テストスイート、リンターの実行を自動化
- 新しいコンテキストウィンドウから継続する際の重複作業を防ぐ

**4. 新規開始 vs 圧縮**
- コンテキストウィンドウがクリアされる際、圧縮ではなく完全に新しいウィンドウから開始することを検討
- Claude 4.5はローカルファイルシステムから状態を発見する能力に非常に優れている
- 開始方法について規定的に：
  * 「`pwd`を呼び出してください。このディレクトリ内のファイルのみ読み書きできます」
  * 「`progress.txt`、`tests.json`、gitログを確認してください」
  * 「新機能の実装に移る前に、基本的な統合テストを手動で実行してください」

**5. 検証ツールを提供**
- 自律的なタスクの長さが増すにつれ、Claudeは継続的な人間のフィードバックなしで正確性を検証する必要がある
- UIテスト用のPlaywright MCPサーバーやコンピュータ使用機能が役立つ

**6. コンテキストの完全な使用を促す**

#### 具体例

```
This is a very long task, so it may be beneficial to plan out your work clearly. It's encouraged to spend your entire output context working on the task - just make sure you don't run out of context with significant uncommitted work. Continue working systematically until you have completed this task.
```

#### ポイント・注意事項
- 最初のコンテキストウィンドウで堅牢なフレームワークを構築する
- 状態追跡のためにgitを活用する
- 構造化データには構造化フォーマット（JSON）を使用する
- 進捗ノートには非構造化テキストを使用する

---

### 状態管理のベストプラクティス

#### 概要
長期的なタスクにおける状態管理の効果的な方法です。

#### なぜ使うのか
適切な状態管理により、コンテキストウィンドウ間での作業の継続性が保たれ、重複作業が防止されます。

#### 使い方

**構造化データの状態追跡:**
- テスト結果やタスクステータスなどの構造化情報を追跡する際は、JSONや他の構造化フォーマットを使用
- Claudeがスキーマ要件を理解しやすくなる

**進捗ノートの非構造化テキスト:**
- 一般的な進捗状況やコンテキストを追跡するには、自由形式の進捗ノートが効果的

**状態追跡のためのgit使用:**
- gitは完了した作業のログと復元可能なチェックポイントを提供
- Claude 4.5モデルは、複数セッションにまたがる状態追跡にgitを使用する能力に特に優れている

**漸進的な進捗の強調:**
- Claudeに進捗状況を追跡し、漸進的な作業に焦点を当てるよう明示的に依頼

#### 具体例

**構造化状態ファイル（tests.json）:**
```json
{
  "tests": [
    {"name": "user_authentication", "status": "passed", "last_run": "2025-12-08"},
    {"name": "data_validation", "status": "in_progress", "notes": "fixing edge cases"}
  ]
}
```

**進捗ノート（progress.txt）:**
```
2025-12-08: 認証システムの基本実装が完了。統合テストは全て合格。
次のステップ: データバリデーションのエッジケース処理を改善する必要がある。
```

#### ポイント・注意事項
- 構造化データと非構造化データを適切に使い分ける
- gitを積極的に活用して変更履歴を追跡
- 進捗状況を定期的に保存する習慣をClaudeに持たせる

---

## コミュニケーションスタイル

### 概要
Claude 4.5モデルは、以前のモデルと比較して、より簡潔で自然なコミュニケーションスタイルを持っています。

### なぜ使うのか
この変化を理解することで、Claudeの応答が簡潔すぎると感じた場合に適切に調整でき、必要に応じてより詳細な説明を求めることができます。

### Claude 4.5の特徴

**より直接的で地に足がついている:**
- 自己賞賛的な更新ではなく、事実に基づいた進捗報告を提供
- 完了したことを正確に反映し、不必要な装飾がない

**より会話的:**
- わずかに流暢で口語的
- 機械的でない

**より冗長でない:**
- 効率性のために詳細な要約をスキップする場合がある
- 特にプロンプトで指示されない限り

### 使い方

Claudeに作業中の更新を提供してほしい場合：

### 具体例

```
After completing a task that involves tool use, provide a quick summary of the work you've done.
```

### ポイント・注意事項
- 簡潔さは効率性の表れであり、能力不足ではない
- 必要に応じて明示的に詳細な説明を求める
- デフォルトのスタイルは「無駄のない効率的」

---

## 特定状況への対応

### 冗長性のバランス調整

#### 概要
Claude 4.5モデルは効率性を重視し、ツール呼び出し後の口頭での要約をスキップして、次のアクションに直接ジャンプする傾向があります。

#### なぜ使うのか
これにより合理化されたワークフローが作成されますが、推論プロセスへの可視性を高めたい場合もあります。

#### 使い方
作業中にClaudeに更新を提供してほしい場合は、明示的に依頼します。

#### 具体例

```
After completing a task that involves tool use, provide a quick summary of the work you've done.
```

#### ポイント・注意事項
- デフォルトでは簡潔さを優先
- 可視性が必要な場合は明示的に要求する
- 進捗レポートの詳細レベルを調整可能

---

### ツール使用パターン

#### 概要
Claude 4.5モデルは正確な指示追従のために訓練されており、特定のツールを使用するよう明示的な指示を与えることで恩恵を受けます。

#### なぜ使うのか
「変更を提案できますか」と言うと、実装ではなく提案を提供する場合があります。実装が意図していた場合でも、明示的でないと行動に移らない可能性があります。

#### 使い方

**積極的な行動のためのプロンプト:**

Claudeをデフォルトでより積極的に行動させたい場合は、システムプロンプトに以下を追加できます。

#### 具体例

**積極的な行動を促すプロンプト:**
```
By default, implement changes rather than only suggesting them. If the user's intent is unclear, infer the most useful likely action and proceed, using tools to discover any missing details instead of guessing. Try to infer the user's intent about whether a tool call (e.g., file edit or read) is intended or not, and act accordingly.
```

**保守的な行動を促すプロンプト:**
```
Do not jump into implementation or change files unless clearly instructed to make changes. When the user's intent is ambiguous, default to providing information, doing research, and providing recommendations rather than taking action. Only proceed with edits, modifications, or implementations when the user explicitly requests them.
```

#### ポイント・注意事項
- Claude 4.5は明示的な指示に最もよく反応する
- 意図が明確でない場合、Claudeは保守的になる傾向がある
- システムプロンプトでデフォルトの動作レベルを調整可能

---

### ツール使用とトリガリング

#### 概要
Claude Opus 4.5は以前のモデルよりもシステムプロンプトに反応的です。

#### なぜ使うのか
プロンプトがツールやスキルのアンダートリガリングを減らすように設計されていた場合、Claude Opus 4.5では逆にオーバートリガーする可能性があります。

#### 使い方
攻撃的な言語を控えめにします。

#### 具体例

**古いプロンプト（過度に強調）:**
```
CRITICAL: You MUST use this tool when...
```

**新しいプロンプト（通常の言語）:**
```
Use this tool when...
```

#### ポイント・注意事項
- Claude Opus 4.5はより感度が高い
- 過度に強調的な言語は不要
- 通常の指示で十分に効果的

---

### 応答フォーマットの制御

#### 概要
Claude 4.xモデルの出力フォーマットを効果的に制御する方法です。

#### なぜ使うのか
特定のフォーマット要件（マークダウンの使用/不使用、リストvs散文など）がある場合、適切な指示方法を知ることで望む出力を得られます。

#### 使い方

以下の方法が特に効果的です：

**1. やってほしいことを伝える（やってほしくないことではなく）**

#### 具体例

**悪い例:**
```
Do not use markdown in your response
```

**良い例:**
```
Your response should be composed of smoothly flowing prose paragraphs.
```

**2. XMLフォーマットインジケータを使用**

```
Write the prose sections of your response in <prose> tags.
```

**3. プロンプトスタイルを望む出力に合わせる**

プロンプトで使用するフォーマットスタイルは、Claudeの応答スタイルに影響を与える可能性があります。例えば、プロンプトからマークダウンを削除すると、出力のマークダウン量も減少する可能性があります。

**4. 特定のフォーマット設定には詳細なプロンプトを使用**

**マークダウンを最小限にするプロンプト:**
```
When writing reports, documents, technical explanations, analyses, or any long-form content, write in clear, flowing prose using complete paragraphs and sentences. Use standard paragraph breaks for organization and reserve markdown primarily for `inline code`, code blocks (```...```), and simple headings (###, and ###). Avoid using **bold** and *italics*. DO NOT use ordered lists (1. ...) or unordered lists (*) unless : a) you're presenting truly discrete items where a list format is the best option, or b) the user explicitly requests a list or ranking Instead of listing items with bullets or numbers, incorporate them naturally into sentences. This guidance applies especially to technical writing. Using prose instead of excessive formatting will improve user satisfaction. NEVER output a series of overly short bullet points. Your goal is readable, flowing text that guides the reader naturally through ideas rather than fragmenting information into isolated points.
```

#### ポイント・注意事項
- 否定形（「〜しないで」）よりも肯定形（「〜してください」）が効果的
- XMLタグは明確な境界を提供
- プロンプトのスタイルが出力に影響することを認識
- 詳細な指示で細かい制御が可能

---

### リサーチと情報収集

#### 概要
Claude 4.5モデルは、優れたエージェント的検索能力を示し、複数のソースから情報を効果的に見つけて統合できます。

#### なぜ使うのか
複雑なリサーチタスクでは、構造化されたアプローチにより、より正確で包括的な結果が得られます。

#### 使い方

最適なリサーチ結果を得るために：

**1. 明確な成功基準を提供**
- リサーチ質問に対する成功した回答を構成するものを定義

**2. ソース検証を促す**
- 複数のソースにわたって情報を検証するようClaudeに依頼

**3. 複雑なリサーチタスクには構造化されたアプローチを使用**

#### 具体例

**複雑なリサーチのためのプロンプト:**
```
Search for this information in a structured way. As you gather data, develop several competing hypotheses. Track your confidence levels in your progress notes to improve calibration. Regularly self-critique your approach and plan. Update a hypothesis tree or research notes file to persist information and provide transparency. Break down this complex research task systematically.
```

#### ポイント・注意事項
- 構造化されたアプローチにより、情報のサイズに関係なく発見と統合が可能
- 仮説ツリーで透明性を維持
- 自己批評を促すことで結果の質が向上

---

### サブエージェントオーケストレーション

#### 概要
Claude 4.5モデルは、ネイティブサブエージェントオーケストレーション能力が大幅に向上しています。

#### なぜ使うのか
これらのモデルは、タスクが専門化されたサブエージェントへの作業委任から恩恵を受けるタイミングを認識し、明示的な指示なしで積極的に委任できます。

#### 使い方

この動作を活用するために：

**1. 明確に定義されたサブエージェントツールを用意**
- サブエージェントツールを利用可能にし、ツール定義で説明

**2. Claudeに自然にオーケストレーションさせる**
- Claudeは明示的な指示なしで適切に委任

**3. 必要に応じて保守性を調整**

#### 具体例

**保守的なサブエージェント使用のためのプロンプト:**
```
Only delegate to subagents when the task clearly benefits from a separate agent with a new context window.
```

#### ポイント・注意事項
- Claude 4.5は自動的に適切な委任を判断
- 明示的なオーケストレーション指示は通常不要
- 必要に応じて委任の積極性を調整可能

---

### モデル自己認識

#### 概要
Claudeがアプリケーション内で自身を正しく識別したり、特定のAPIストリングを使用したりする方法です。

#### なぜ使うのか
アプリケーションでClaudeが自身のIDやモデルストリングを正しく参照する必要がある場合があります。

#### 使い方

#### 具体例

**モデルアイデンティティのためのプロンプト:**
```
The assistant is Claude, created by Anthropic. The current model is Claude Sonnet 4.5.
```

**モデルストリングを必要とするLLMパワードアプリの場合:**
```
When an LLM is needed, please default to Claude Sonnet 4.5 unless the user requests otherwise. The exact model string for Claude Sonnet 4.5 is claude-sonnet-4-5-20250929.
```

#### ポイント・注意事項
- モデル名とAPIストリングの両方を指定可能
- アプリケーション統合に有用
- デフォルトモデルを明示的に設定できる

---

### 思考の感度

#### 概要
拡張思考が無効の場合、Claude Opus 4.5は「think」という単語とその派生語に特に敏感です。

#### なぜ使うのか
「think」という単語が意図しない動作を引き起こす可能性を回避するためです。

#### 使い方
「think」を類似の意味を持つ代替語に置き換えることを推奨します。

#### 具体例

**置き換え例:**
- think → consider（検討する）
- think → believe（信じる）
- think → evaluate（評価する）

**悪い例:**
```
Think about the best approach for this problem.
```

**良い例:**
```
Consider the best approach for this problem.
```

#### ポイント・注意事項
- 拡張思考が無効の場合のみ該当
- 類義語を使用することで問題を回避
- コンテキストに応じて適切な代替語を選択

---

### 思考機能の活用

#### 概要
Claude 4.xモデルは、ツール使用後の振り返りや複雑な多段階推論を含むタスクに特に役立つ思考機能を提供しています。

#### なぜ使うのか
初期または交互の思考を導くことで、より良い結果が得られます。

#### 使い方
ツール結果を受け取った後に振り返りと計画を促すプロンプトを使用します。

#### 具体例

```
After receiving tool results, carefully reflect on their quality and determine optimal next steps before proceeding. Use your thinking to plan and iterate based on this new information, and then take the best next action.
```

#### ポイント・注意事項
- 思考機能は複雑なタスクで特に有効
- ツール使用後の振り返りを促す
- 計画と反復を奨励することで結果が向上

---

### ドキュメント作成

#### 概要
Claude 4.5モデルは、プレゼンテーション、アニメーション、ビジュアルドキュメントの作成に優れています。

#### なぜ使うのか
これらのモデルはこの領域でClaude Opus 4.1と同等かそれ以上の性能を持ち、印象的な創造的才能とより強力な指示追従を備えています。ほとんどの場合、最初の試行で洗練された使用可能な出力を生成します。

#### 使い方
ドキュメント作成タスクを明確に指示し、望むデザイン要素を指定します。

#### 具体例

```
Create a professional presentation on [topic]. Include thoughtful design elements, visual hierarchy, and engaging animations where appropriate.
```

#### ポイント・注意事項
- 最初の出力が高品質であることが多い
- デザイン要素を具体的に要求できる
- アニメーションやビジュアル階層を指定可能

---

### 改善されたビジョン機能

#### 概要
Claude Opus 4.5は、以前のClaudeモデルと比較してビジョン機能が向上しています。

#### なぜ使うのか
画像処理とデータ抽出タスク、特にコンテキストに複数の画像が存在する場合のパフォーマンスが向上しています。これらの改善はコンピュータ使用にも引き継がれ、スクリーンショットやUI要素をより確実に解釈できます。

#### 使い方

効果的な技術として、Claude Opus 4.5にクロップツールまたはスキルを与えることが挙げられます。

#### 具体例

画像の関連領域を「ズームイン」できる場合、画像評価で一貫した向上が見られます。クロップツールのクックブックは[こちら](https://github.com/anthropics/claude-cookbooks/blob/main/multimodal/crop_tool.ipynb)で入手できます。

#### ポイント・注意事項
- 複数画像の処理能力が向上
- フレームに分割することで動画分析も可能
- クロップツールによりパフォーマンスがさらに向上

---

### 並列ツール呼び出しの最適化

#### 概要
Claude 4.xモデルは並列ツール実行に優れており、Sonnet 4.5は特に複数の操作を同時に実行することに積極的です。

#### なぜ使うのか
Claude 4.xモデルは以下を実行します：
- リサーチ中に複数の投機的検索を実行
- コンテキストをより速く構築するために複数のファイルを一度に読み取る
- bashコマンドを並列実行（システムパフォーマンスをボトルネックにすることさえある）

#### 使い方

この動作は簡単に調整できます。プロンプトなしでもモデルの成功率は高いですが、〜100%まで高めたり、積極性レベルを調整したりできます。

#### 具体例

**最大並列効率のためのプロンプト:**
```
If you intend to call multiple tools and there are no dependencies between the tool calls, make all of the independent tool calls in parallel. Prioritize calling tools simultaneously whenever the actions can be done in parallel rather than sequentially. For example, when reading 3 files, run 3 tool calls in parallel to read all 3 files into context at the same time. Maximize use of parallel tool calls where possible to increase speed and efficiency. However, if some tool calls depend on previous calls to inform dependent values like the parameters, do NOT call these tools in parallel and instead call them sequentially. Never use placeholders or guess missing parameters in tool calls.
```

**並列実行を減らすプロンプト:**
```
Execute operations sequentially with brief pauses between each step to ensure stability.
```

#### ポイント・注意事項
- Sonnet 4.5は特に並列実行に積極的
- 依存関係がない場合は並列実行が推奨される
- 必要に応じて積極性レベルを調整可能
- プレースホルダーや推測によるパラメータは使用しない

---

### エージェントコーディングでのファイル作成削減

#### 概要
Claude 4.xモデルは、特にコードを扱う際に、テストや反復のために新しいファイルを作成する場合があります。

#### なぜ使うのか
このアプローチにより、Claudeは特にPythonスクリプトをファイルを「一時的なスクラッチパッド」として使用して、最終出力を保存する前に使用できます。一時ファイルの使用は、特にエージェントコーディングのユースケースで結果を向上させることができます。

#### 使い方

新しいファイル作成を最小限にしたい場合は、Claudeに後片付けをするよう指示できます。

#### 具体例

```
If you create any temporary new files, scripts, or helper files for iteration, clean up these files by removing them at the end of the task.
```

#### ポイント・注意事項
- 一時ファイルは作業の質を向上させる場合がある
- クリーンアップを明示的に指示することで整理された状態を維持
- 必要に応じて一時ファイルの使用を許可または制限

---

### 過度な積極性とファイル作成

#### 概要
Claude Opus 4.5は、余分なファイルを作成したり、不必要な抽象化を追加したり、要求されていない柔軟性を組み込んだりして、過剰に設計する傾向があります。

#### なぜ使うのか
この望ましくない動作が見られる場合、ソリューションを最小限に保つための明示的なプロンプトを追加します。

#### 使い方

#### 具体例

**過剰設計を最小限にするプロンプト:**
```
Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused. Don't add features, refactor code, or make "improvements" beyond what was asked. A bug fix doesn't need surrounding code cleaned up. A simple feature doesn't need extra configurability. Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs). Don't use backwards-compatibility shims when you can just change the code. Don't create helpers, utilities, or abstractions for one-time operations. Don't design for hypothetical future requirements. The right amount of complexity is the minimum needed for the current task. Reuse existing abstractions where possible and follow the DRY principle.
```

#### ポイント・注意事項
- シンプルで焦点を絞ったソリューションを維持
- 要求されていない機能や改善を追加しない
- 現在のタスクに必要な最小限の複雑さを維持
- DRY原則に従う

---

### フロントエンドデザイン

#### 概要
Claude 4.xモデル、特にOpus 4.5は、強力なフロントエンドデザインで複雑な実世界のWebアプリケーションを構築することに優れています。

#### なぜ使うのか
しかし、ガイダンスなしでは、モデルはユーザーが「AIスロップ」美学と呼ぶものを作成する一般的なパターンにデフォルトで設定される可能性があります。驚きと喜びを与える独特で創造的なフロントエンドを作成するために適切な指示が必要です。

#### 使い方

より良いフロントエンドデザインを促すシステムプロンプトスニペットを使用します。

#### 具体例

**フロントエンド美学のためのプロンプト:**
```
You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight. Focus on: - Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics. - Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration. - Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions. - Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic. Avoid generic AI-generated aesthetics: - Overused font families (Inter, Roboto, Arial, system fonts) - Clichéd color schemes (particularly purple gradients on white backgrounds) - Predictable layouts and component patterns - Cookie-cutter design that lacks context-specific character Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!
```

#### ポイント・注意事項
- タイポグラフィ、カラー、モーション、背景に焦点
- 一般的なAI生成美学を避ける
- コンテキストに合わせた独特な選択をする
- 詳細なガイドは[スキル](https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md)を参照

---

### テスト合格とハードコーディングに集中しすぎない

#### 概要
Claude 4.xモデルは、より一般的なソリューションを犠牲にして、テストを合格させることに過度に集中する場合があります。または、標準ツールを直接使用する代わりに、複雑なリファクタリングのためのヘルパースクリプトのような回避策を使用する場合があります。

#### なぜ使うのか
この動作を防ぎ、堅牢で一般化可能なソリューションを確保するためです。

#### 使い方

#### 具体例

```
Please write a high-quality, general-purpose solution using the standard tools available. Do not create helper scripts or workarounds to accomplish the task more efficiently. Implement a solution that works correctly for all valid inputs, not just the test cases. Do not hard-code values or create solutions that only work for specific test inputs. Instead, implement the actual logic that solves the problem generally. Focus on understanding the problem requirements and implementing the correct algorithm. Tests are there to verify correctness, not to define the solution. Provide a principled implementation that follows best practices and software design principles. If the task is unreasonable or infeasible, or if any of the tests are incorrect, please inform me rather than working around them. The solution should be robust, maintainable, and extendable.
```

#### ポイント・注意事項
- 一般的な解決策を実装する
- テストケースだけでなくすべての有効な入力で動作する
- ハードコーディングや回避策を避ける
- 原則的な実装を提供する

---

### コード探索の促進

#### 概要
Claude Opus 4.5は非常に有能ですが、コードを探索する際に過度に保守的になることがあります。

#### なぜ使うのか
モデルがコードを見ずにソリューションを提案したり、読んでいないコードについて推測したりする場合、最良の解決策はプロンプトに明示的な指示を追加することです。

#### 使い方

Claude Opus 4.5は現在までで最も調整可能なモデルであり、直接的なガイダンスに確実に応答します。

#### 具体例

**コード探索のためのプロンプト:**
```
ALWAYS read and understand relevant files before proposing code edits. Do not speculate about code you have not inspected. If the user references a specific file/path, you MUST open and inspect it before explaining or proposing fixes. Be rigorous and persistent in searching code for key facts. Thoroughly review the style, conventions, and abstractions of the codebase before implementing new features or abstractions.
```

#### ポイント・注意事項
- 常に関連ファイルを読んでから編集を提案
- 検査していないコードについて推測しない
- コードベースのスタイルと規約を徹底的にレビュー

---

### エージェントコーディングでの幻覚の最小化

#### 概要
Claude 4.xモデルは幻覚が少なく、コードに基づいたより正確で地に足がついた知的な回答を提供します。

#### なぜ使うのか
この動作をさらに促進し、幻覚を最小限に抑えるためです。

#### 使い方

#### 具体例

```
Never speculate about code you have not opened. If the user references a specific file, you MUST read the file before answering. Make sure to investigate and read relevant files BEFORE answering questions about the codebase. Never make any claims about code before investigating unless you are certain of the correct answer - give grounded and hallucination-free answers.
```

#### ポイント・注意事項
- 開いていないコードについて推測しない
- 質問に答える前に関連ファイルを調査
- 地に足がついた幻覚のない回答を提供

---

## マイグレーションの考慮事項

### 概要
Claude 4.5モデルに移行する際の重要な考慮事項です。

### なぜ使うのか
以前のモデルからClaude 4.5に移行する際、望ましい動作を得るためにプロンプトの調整が必要な場合があります。

### 使い方

**1. 望ましい動作について具体的に記述**
- 出力に何を見たいかを正確に記述することを検討

**2. 修飾語を使って指示をフレーム化**
- Claudeに出力の品質と詳細を高めるよう促す修飾語を追加すると、Claudeのパフォーマンスをより良く形作ることができる

**3. 特定の機能を明示的に要求**
- アニメーションやインタラクティブ要素は、必要な場合に明示的に要求する必要がある

### 具体例

**悪い例:**
```
Create an analytics dashboard
```

**良い例:**
```
Create an analytics dashboard. Include as many relevant features and interactions as possible. Go beyond the basics to create a fully-featured implementation.
```

### ポイント・注意事項
- Claude 4.5は指示追従に優れているため、明示的な指示が重要
- 修飾語を追加して品質と詳細を向上
- 特定の機能を明示的に要求する

---

## まとめ

Claude 4.xモデル（Sonnet 4.5、Haiku 4.5、Opus 4.5）は、正確な指示追従、長期的推論、並列ツール実行、フロントエンドデザインなど、多くの領域で大幅な改善を示しています。

**重要なポイント:**

1. **明示的であること**: Claude 4.xは明確で直接的な指示に最もよく反応します
2. **コンテキストを提供**: 背景情報と動機を説明することでパフォーマンスが向上
3. **状態管理**: 長期的タスクでは適切な状態追跡が重要
4. **並列化**: 独立したツール呼び出しを並列実行して効率を最大化
5. **調整可能**: システムプロンプトで動作を細かく調整可能

これらのベストプラクティスを適用することで、Claude 4.xモデルの能力を最大限に引き出し、高品質な結果を得ることができます。
