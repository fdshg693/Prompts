# プロンプトファイルの構造
READMEなどのドキュメントファイルを除いて、各プロンプトファイルは以下のような構造を持っています。

```xml
<use_case>
  {{use_case_description}}
</use_case>

<prompt_template>
  {{content}}
</prompt_template>

<english_translation>
  {{english_content}}
</english_translation>

<variables>
  {{variable_definitions}}
</variables>

<prompt_explanation>
  {{explanation_content}}
</prompt_explanation>
```

## use_case
`<use_case>` タグ内には、そのプロンプトが想定されるユースケースや利用シナリオの説明が記載されています。

## prompt_template
`<prompt_template>` タグ内には、実際にAIに渡すプロンプトのテンプレートが記載されています。  
変数は `{{VARIABLE_NAME}}` の形式で埋め込まれ、後述の `<variables>` セクションで定義された内容に置き換えられます。  

## english_translation
`<english_translation>` タグ内には、`<prompt_template>` の英語翻訳が記載されています。

## variables
`<variables>` タグ内には、プロンプトテンプレートで使用される変数の定義が記載されています。  
YAML形式で記述され、各変数の説明やデフォルト値が含まれます。  

例：
```yaml
VARIABLE_NAME:
  description: 変数の説明
  example: 例となる値
```
※ `description`と`example`は必須です。他のフィールドは利用不可です。

## prompt_explanation
`<prompt_explanation>` タグ内には、プロンプトの目的や使用方法、注意点などの説明が記載されています。  