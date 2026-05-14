# NetArticle — 記事執筆ハーネス

ワークスペース内のソースをもとに、Claude Code が **Qiita / Note / Zenn** 向けの記事を自動生成するためのharness。

## 使い方

入力ソースは2通り：

### A. ローカルモード（推奨）
1. `sources/{project-name}/` フォルダを作り、記事のもとになるファイルを置く（コード・README・メモ・下書き・画像など）
2. Claude Code を起動し「`sources/my-app` から Qiita 記事を書いて」のように指示

### B. 外部パスモード
ObsidianのVaultや別の場所にあるプロジェクトを資料として参照できる（**読み取り専用**、書き込みは行わない）。
- 「Obsidian の `D:\ObsidianVault\TechNotes\react-hooks` を元に Zenn 記事を書いて」
- 「`~/projects/my-app` を読み込んで Qiita 記事にして」

Vaultなど大きい場合は、特定ファイル・サブフォルダ・タグでスコープ指定するとよい。

### 共通フロー
3. 対話に従ってプラットフォーム・文体・構成を確定する
4. 完成記事が `articles/{platform}/{slug}.md`（このharness内）に保存される

## プラットフォーム

| プラットフォーム | 出力先 | 特徴 |
|---|---|---|
| Qiita | `articles/qiita/` | 技術記事・タグ・`:::note` 記法 |
| Note | `articles/note/` | 読み物寄り・ハッシュタグ・文章主体 |
| Zenn | `articles/zenn/` | 技術記事・topics/type・章立て |

## ディレクトリ構成

```
NetArticle/
├── .claude/skills/article-writer/   # スキル本体
│   ├── SKILL.md
│   └── agents/
│       ├── analyzer.md
│       ├── writer-qiita.md
│       ├── writer-note.md
│       └── writer-zenn.md
├── sources/        # 入力（ユーザーが配置）
└── articles/       # 出力
    ├── qiita/
    ├── note/
    └── zenn/
```

自動投稿は行いません。生成されたMarkdownを各プラットフォームに手動で投稿してください。
