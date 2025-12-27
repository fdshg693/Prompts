# プロンプトファイルの構造
READMEなどのドキュメントファイルを除いて、各プロンプトファイルは以下のような構造を持っています。

````markdown
- use_case
```markdown
{{use_case_description}}
```

- other_ai_settings
```yaml
{{other_settings}}
```

- prompt_template
```markdown
{{content}}
```

- english_translation
```markdown
{{english_content}}
```

- variables
```yaml
{{variable_definitions}}
```

- prompt_explanation
```markdown
{{explanation_content}}
```

- other_settings_explanation
```markdown
{{other_settings_explanation}}
```
````

## use_case
`use_case` セクションには、そのプロンプトが想定されるユースケースや利用シナリオの説明が記載されています。

## prompt_template
`prompt_template` セクションには、実際にAIに渡すプロンプトのテンプレートが記載されています。  
変数は `{{VARIABLE_NAME}}` の形式で埋め込まれ、後述の `variables` セクションで定義された内容に置き換えられます。  

## english_translation
`english_translation` セクションには、`prompt_template` の英語翻訳が記載されています。

## variables
`variables` セクションには、プロンプトテンプレートで使用される変数の定義が記載されています。  
YAML形式で記述され、各変数の説明やデフォルト値が含まれます。  

例：
```yaml
VARIABLE_NAME:
  description: 変数の説明
  example: 例となる値
```
※ `description`と`example`は必須です。他のフィールドは利用不可です。

## prompt_explanation
`prompt_explanation` セクションには、プロンプトの目的や使用方法、注意点などの説明が記載されています。  

## other_ai_settings
`other_ai_settings` セクションには、プロンプト以外のAI設定（例: モデル名、利用可能ツール、他AIツールがサポートする設定など）が記載されています。

## other_settings_explanation
`other_settings_explanation` セクションには、`other_ai_settings` セクションで記載された設定の説明が記載されています。