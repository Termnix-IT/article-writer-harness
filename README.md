# NetArticle — 記事執筆ハーネス

ワークスペース内のソースをもとに、Claude Code が **Qiita / Note / Zenn** 向けの記事を自動生成するためのharness。

## 使い方

1. `sources/{project-name}/` フォルダを作り、記事のもとになるファイルを置く
   - コード、README、メモ、下書き（`.md` / `.txt`）、画像など
2. Claude Code を起動し、以下のように指示する
   - 「`sources/my-app` から Qiita 記事を書いて」
   - 「Note用にこのフォルダから書いて」
   - 「Zennで投稿したい」
3. 対話に従ってプラットフォーム・文体・構成を確定する
4. 完成記事が `articles/{platform}/{slug}.md` に保存される

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
