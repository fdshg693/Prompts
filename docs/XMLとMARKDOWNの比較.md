# XML in AI prompts: widely supported, not universally required

**All three major AI providers—Anthropic, OpenAI, and Google—officially support XML tags for prompt structuring, though recommendations vary in emphasis.** Anthropic is the strongest advocate, explicitly stating XML "tends to lead to the best performance" due to Claude's training. OpenAI validates XML as "performing well," particularly for long-context scenarios, while recommending Markdown as a starting point. Google treats XML and Markdown as equally effective options. The evidence shows XML is a legitimate, well-supported format across providers—but it is not a universal "gold standard" and alternative formats are explicitly endorsed by all three companies.

## Anthropic champions XML most enthusiastically

Anthropic's official documentation dedicates an entire page to XML usage, making it the most vocal proponent of this format. Their position is unambiguous: "When your prompts involve multiple components like context, instructions, and examples, XML tags can be a game-changer. They help Claude parse your prompts more accurately, leading to higher-quality outputs."

The technical rationale is explicit. Anthropic's legacy model guide states that while "Claude can also recognize other structured formats like JSON and Markdown, **XML tends to lead to the best performance in most cases due to exposure during training**." This training-specific advantage distinguishes Anthropic's recommendation from the others.

Anthropic prescribes specific best practices: use consistent tag names throughout prompts, nest tags for hierarchical content (`<outer><inner></inner></outer>`), and choose semantic tag names that make sense with the surrounded information. They recommend combining XML with other techniques—using `<examples>` for multishot prompting and `<thinking>` / `<answer>` tags for chain-of-thought reasoning.

Common tags suggested include `<instructions>`, `<context>`, `<examples>`, `<doc>`, `<response>`, and domain-specific tags like `<tax_code>` or `<contract>`. A September 2025 engineering article notes that "the exact formatting of prompts is likely becoming less important as models become more capable"—suggesting XML's dominance may diminish over time.

## OpenAI recommends Markdown first, validates XML for complex scenarios

OpenAI's GPT-4.1 and GPT-5 prompting guides present a more nuanced hierarchy. **Markdown is their primary recommendation** for general use: "We recommend starting here, and using markdown titles for major sections and subsections."

However, XML receives explicit endorsement for specific use cases. The GPT-4.1 guide states: "XML: These also perform well, and we have improved adherence to information in XML with this model. XML is convenient to precisely wrap a section including start and end, add metadata to the tags for additional context, and enable nesting."

For long-context scenarios, OpenAI's testing reveals important performance differences. **XML performs well in long-context testing**, while **JSON performs "particularly poorly"** in these scenarios. This makes XML the superior choice when handling large documents or extensive context windows.

Real-world validation comes from Cursor's implementation: "In Cursor's testing, using structured XML specs like `<[instruction]_spec>` improved instruction adherence on their prompts." OpenAI's documentation includes concrete XML examples:

```xml
<examples>
<example1 type="Abbreviate">
<input>San Francisco</input>
<output>- SF</output>
</example1>
</examples>
```

The key caveat: "if you're retrieving documents that contain lots of XML, an XML-based delimiter will likely be less effective." Format selection should consider the content being processed.

## Google positions XML and Markdown as equal options

Google's Gemini documentation, updated as recently as November-December 2025, explicitly recommends both formats without hierarchy: "Use consistent structure: Employ clear delimiters to separate different parts of your prompt. **XML-style tags (e.g., `<context>`, `<task>`) or Markdown headings are effective.** Choose one format and use it consistently within a single prompt."

Google provides detailed XML-based templates in their official documentation, including structured examples with tags like `<role>`, `<constraints>`, `<context>`, `<task>`, `<output_format>`, and `<instructions>`. Their Vertex AI documentation offers a comprehensive prompt template using XML structure throughout.

For Gemini Nano specifically, Google notes that `##` delimiters between components are "particularly critical," suggesting hardware-constrained models may have different formatting sensitivities.

Google emphasizes consistency above format choice. Their few-shot example guidance warns: "Make sure that the structure and formatting of few-shot examples are the same to avoid responses with undesired formats... especially paying attention to XML tags, white spaces, newlines, and example splitters."

## Cross-provider comparison reveals key patterns

| Provider | Primary Format | XML Support Level | Best XML Use Cases |
|----------|---------------|-------------------|-------------------|
| Anthropic | XML | Strong recommendation | All complex prompts; trained specifically on XML |
| OpenAI | Markdown | Validated as effective | Long context, nesting, metadata attributes |
| Google | XML or Markdown | Equal recommendation | Complex structure, consistent organization |

**Convergence points** across all three providers include support for XML as a valid structuring mechanism, recognition that clear delimiters improve prompt parsing, acknowledgment that nesting and hierarchy benefit from structured formats, and recommendations for consistency within individual prompts.

**Divergence points** center on the degree of endorsement. Anthropic uniquely cites training exposure as the reason for XML's effectiveness. OpenAI specifically warns against JSON for long-context scenarios while endorsing XML. Google maintains strict format agnosticism between XML and Markdown.

## The verdict: XML is legitimate but not the sole standard

The research reveals that **XML is a well-supported, officially recommended format across all major AI providers**—but calling it "the gold standard" overstates the consensus. A more accurate characterization:

- **Anthropic**: XML is the preferred format due to training optimization
- **OpenAI**: XML is one of several good options, best for complex/long-context scenarios
- **Google**: XML and Markdown are interchangeable; consistency matters more than format choice

For practitioners, the evidence suggests XML is an excellent choice when working with Claude models specifically, when handling long-context documents (per OpenAI's testing), when prompts require nesting or hierarchical structure, or when metadata attributes would aid clarity. However, **Markdown remains equally valid** for simpler prompts, shorter contexts, and when working primarily with OpenAI or Google models.

The most important principle across all documentation is **consistency**—whichever format you choose, use it uniformly throughout your prompts. As models continue advancing, Anthropic's September 2025 note that "exact formatting is likely becoming less important" suggests format sensitivity may decrease, making this a transitional rather than permanent consideration.

## Conclusion

XML has earned legitimate status as a prompt structuring format with official backing from Anthropic, OpenAI, and Google. Anthropic's documentation provides the strongest case for XML's superiority, explicitly tied to Claude's training data exposure. OpenAI and Google validate XML without positioning it as superior to alternatives like Markdown. The practical takeaway: **XML is a proven, cross-platform formatting approach**—but the "best" format depends on your specific model, context length, and prompt complexity rather than any universal standard.