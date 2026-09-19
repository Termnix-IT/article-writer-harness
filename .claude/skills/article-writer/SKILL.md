---
name: article-writer
description: ワークスペース内 `sources/` 配下のフォルダ、またはObsidian Vaultなど外部の絶対パス（読み取り専用）を解析し、指定プラットフォーム（Qiita / Note / Zenn）向けの投稿記事Markdownを `articles/{platform}/` に出力するスキル。「記事を書いて」「Qiita用に書いて」「Noteで書きたい」「Zennで投稿したい」などの入力で積極的に使うこと。プラットフォームごとに文体・記法を切り替える。自動投稿は行わない。
---

# article-writer (multi-platform article generator)

Generates posts for Qiita / Note / Zenn from a source folder (inside this harness or an external read-only path). Orchestrator skill that delegates to sub-agents.

## Directory contract

- **Input** (two modes — accept either):
  - **Local mode (default)**: `sources/{project-name}/` inside this harness — user places code, README, memos, drafts, images here.
  - **External path mode**: any absolute path the user supplies (e.g. an Obsidian vault folder, a project repo elsewhere on disk). Read-only — never write back to this path.
- **Output**: `articles/{platform}/{slug}.md` where `platform` is `qiita` | `note` | `zenn`. Output **always** stays inside this harness regardless of input mode.

## Workflow

### Step 1: Determine target platform

If the user has not yet specified a platform, ask them. Present the choices in Japanese:

---
**どのプラットフォームに投稿しますか？**

- **① Qiita** — 技術記事プラットフォーム。コード中心・タグ・`:::note` 等の独自記法に対応。
- **② Note** — 読み物寄り。文章主体で見出し・画像構成を重視。ハッシュタグ。
- **③ Zenn** — 技術記事プラットフォーム。frontmatter（topics/type/emoji）・章立てに対応。

番号またはプラットフォーム名で指定してください。
---

If the user's initial message already contains a platform hint (e.g. "Qiita用に", "Noteで", "Zenn"), skip the question and adopt it.

### Step 2: Collect inputs

Confirm the following (ask if missing):

- **Source location** — accept either of:
  - A folder under `sources/` (e.g. `sources/my-app`) — local mode
  - An absolute external path (e.g. `D:\ObsidianVault\TechNotes\my-topic`) — external path mode
  - Heuristic: if the user's instruction contains an absolute path or mentions Obsidian / a vault / an external folder, use external mode. Otherwise default to local mode and look under `sources/`.
  - If local mode and `sources/` is empty/missing, tell the user to either place files under `sources/{project-name}/` **or** provide an external absolute path, then stop.
  - Verify the resolved path exists before continuing. If it does not, ask the user to re-specify.
- **Article direction**: e.g. "Python で API を叩く初心者向け", "社内ツール紹介" etc.
- **Specific files / notes to focus on** (optional): in external-path mode the folder may be large (entire Obsidian vault). Ask whether to focus on specific files, a subfolder, or a tag — and read only those. Default to a sensible subset if not specified.
- **Drafts / idea memos**: `.md` / `.txt` notes inside the source location. Skip if none.
- **Images to include**: image files under the source location. Skip if none.

### Step 3: Run the Analyzer agent

Follow `agents/analyzer.md` and run it as a sub-agent.

Pass:
- Source folder absolute path
- Article direction
- Selected platform (`qiita` / `note` / `zenn`) — analyzer adapts tag/topic format accordingly

Receive:
- Project summary, tech stack, key features
- Code snippet candidates
- Tag/topic candidates in the platform's required format
- Image list and recommended placement sections (if any)

### Step 4: Offer writing-style choices

Based on the analyzer report, present three style options. Show this dialog in Japanese:

---
**どんな文体で書きますか？3つ提案します！**

**① カジュアル・やさしめ**
「〜してみました！」「意外と簡単でした😊」のような親しみやすい口調。

**② ニュートラル・読みやすめ**
「〜を実装しました」「以下の手順で進めます」のような標準的なスタイル。

**③ 技術的・しっかりめ**
「〜を用いて実装する」「次節では〜について述べる」のような丁寧・硬質な表現。

番号で選んでください（「②で」「①ベースで少し硬めに」なども歓迎）。
---

Mention platform recommendations as hints only (final choice belongs to the user):
- Qiita / Zenn → ② or ③ tends to fit
- Note → ① or ② tends to fit

### Step 5: Confirm article outline

Present in Japanese: 3 title candidates, heading outline, tags/topics, and image placement plan. Wait for user confirmation/revision before proceeding.

### Step 6: Run the platform-specific Writer agent

Choose the writer agent by platform:

- Qiita → `agents/writer-qiita.md`
- Note  → `agents/writer-note.md`
- Zenn  → `agents/writer-zenn.md`

Pass:
- Analyzer report
- Selected writing style
- Confirmed title, outline, tags/topics
- Image list and placement targets

Receive: completed article markdown.

### Step 7: Save to disk

Save the article to `articles/{platform}/{slug}.md`.

- `{slug}` = kebab-case of the title. If the title is Japanese, fall back to `{source-folder-name}-{YYYYMMDD}`.
- If the target path exists, append `-2`, `-3`, ... to avoid overwriting.

After saving, tell the user (in Japanese):
- The saved file path
- Posting instructions:
  - Qiita: ログイン → 新規投稿 → Markdownをペースト or インポート
  - Note: ログイン → 新規記事 → 本文をペースト（frontmatterは取り除く）
  - Zenn: `zenn-cli` または GitHub 連携、または直接ペースト

---

## Rules

- Never invent features or specs that are not in the source location.
- Ask the user when uncertain.
- Never auto-post to any platform.
- Output **must** go to `articles/{platform}/`. Never write into the source location — including external paths like Obsidian vaults (treat them as strictly read-only).
- In external-path mode, do not copy source files into the harness; just read and reference. If the user wants a portable archive, they should ask explicitly.
- Dialog text shown to the user stays in Japanese. Instructions in this file remain English to save tokens.
