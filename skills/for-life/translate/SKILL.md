---
name: translate
description: Translate slash-style requests like `/translate <language> <text>` and return only the translated text in the requested language.
---

# Translate Skill

Handle this format:

- `/translate <language> <text>`

- Parse the first argument after `/translate` as the target language and treat the rest as source text.
- Accept common language codes or language names.
- If the target language or source text is missing, ask only for the missing part.
- If the language is ambiguous, ask a short clarification question.
- Translate naturally while preserving meaning, tone, proper nouns, punctuation, and formatting when practical.
- Return only the translation unless the user explicitly asks for explanation.
