---
name: translate
description: Translate user-provided text into a target language from slash-command style input. Use when the user writes commands like `/translate <language> <text>` (for example `/translate en こんにちは`) and expects the assistant to return only the translation in the requested language.
---

# Translate Skill

Parse and execute translation requests in this format:

- `/translate <language> <text>`

## Workflow

1. Parse command arguments.
   - Read the first argument after `/translate` as the target language.
   - Treat all remaining content as the source text.
2. Validate inputs.
   - If the target language is missing, ask for it.
   - If the source text is missing, ask for it.
3. Interpret the language argument.
   - Accept common language codes (`en`, `ja`, `fr`, `de`, `es`, etc.).
   - Accept natural language names (`English`, `Japanese`, `French`, etc.).
   - If ambiguous, ask a short clarification question.
4. Translate source text into the target language.
   - Preserve original meaning, tone, and intent.
   - Preserve proper nouns unless transliteration is clearly better.
   - Keep punctuation and formatting when possible.
5. Return output.
   - Return only the translated text by default.
   - Add brief notes only when the user explicitly asks for explanation.

## Output Rules

- Do not include analysis unless asked.
- Do not include source text unless asked.
- For single words, return a concise equivalent.
- For full sentences or paragraphs, return natural phrasing in the target language.

## Examples

- Input: `/translate en こんにちは`
  Output: `Hello`

- Input: `/translate ja I appreciate your quick response.`
  Output: `迅速なご返信に感謝します。`

- Input: `/translate French This is a test.`
  Output: `Ceci est un test.`
