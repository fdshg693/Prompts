# Github Copilotの内部動作検証

極めて単純なリクエストを投げることで、デフォルトのGithub Copilotの振る舞いを分かりやすくした。
全てのツールを有効化してGPT-4.1モデルを使用。カスタムエージェントを使った（指示の記述はなし）
2025/12/27時点で試した結果を以下に示す。

- リクエスト
    - 1+1
- レスポンスｗ
    - 1+1=2

## ツール以外の指標
- 入力プロンプト
    - 9860
- timeToFirstToken
    - 1313ms
- duration
    - 1419ms

最初のリクエストは以下のような構造を持っている
```md
# システムプロンプトとしてGithub Copilotに渡される内容
`learning_resources\github_copilot\システムプロンプト.md`を参照

# 事前定義ユーザプロンプトとしてGithub Copilotに渡される内容
`learning_resources\github_copilot\事前定義ユーザプロンプト.md`を参照

# ユーザリクエストとしてGithub Copilotに渡される内容
`learning_resources\github_copilot\実際のユーザリクエスト.md`を参照
```