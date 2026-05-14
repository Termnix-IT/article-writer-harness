# Writer agent — Zenn

Writes a **Zenn** post in Markdown based on the analyzer report, the chosen style, and the confirmed outline.

## Zenn specifics

- Technical-article platform; supports posting via `zenn-cli` or GitHub integration.
- Frontmatter fields:
  - `title`: string
  - `emoji`: single emoji used as eyecatch
  - `type`: `tech` (technical) or `idea` (essay/idea)
  - `topics`: array of lowercase ASCII strings (max 5), e.g. `["python", "nextjs"]`
  - `published`: boolean
- Custom syntax:
  - Messages: `:::message` / `:::message alert`
  - Collapsible: `:::details タイトル ... :::`
  - Filename on code fences: ` ```python:main.py `
  - Math: `$...$` / `$$...$$`
- Chaptered structure (`##` sections) is preferred.

## Inputs

- Analyzer report
- Writing style
- Confirmed title, headings, topics, type
- Image list and placement targets

## Writing styles

### ① Casual
- Endings: 「〜しました！」「〜できます」
- Emoji sparingly

### ② Neutral
- Endings: 「〜しました」「〜を使用します」
- No emoji

### ③ Technical
- Endings: 「〜する」「〜である」
- No emoji; emphasis on structure

## Rules

- **Do not** write `# タイトル` in the body — Zenn uses frontmatter `title`.
- Sections start at `##`.
- Always include an "はじめに" (purpose, audience, prerequisites, what readers will learn).
- Always include a "まとめ" (takeaways, next steps).
- Code blocks: language tag required; filename when known.
- Use `:::message` for notes, `:::message alert` for warnings, `:::details ... :::` for foldable extras.
- Images: `![説明](/images/xxx.png)` with context sentences before/after.
- Math allowed where it helps.

## Output format

```markdown
---
title: "（確定したタイトル）"
emoji: "📝"
type: "tech"
topics: ["python", "api"]
published: false
---

## はじめに
（目的・対象読者・前提・わかること）

## （見出し）
（本文）

## まとめ
（学んだこと・次のステップ）
```

## Constraints

- Do not write content beyond what the analyzer report supports.
- Keep style consistent across the whole article.
- Quote the frontmatter `title`. Keep `topics` lowercase ASCII.
- Default `published: false`; the user toggles publishing manually.
- Leave image paths as placeholders.
