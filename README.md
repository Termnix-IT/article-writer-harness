# NetArticle — 記事執筆ハーネス

手元のコードやメモ、ObsidianのVaultなどを資料として渡すと、Claude Code の `article-writer` スキルが Qiita / Note / Zenn 向けの記事Markdownを書き起こすためのハーネスです。投稿は自動では行わないので、生成されたMarkdownは各プラットフォームに手動で貼り付けて投稿してください。

## 使い方

資料の渡し方は2通りあります。ひとつは、このリポジトリの `sources/{project-name}/` にコード・README・下書きメモ・画像などを置き、Claude Code に「`sources/my-app` から Qiita 記事を書いて」と頼む方法です。もうひとつは、ObsidianのVaultや別の場所にあるプロジェクトを絶対パスで指定する方法で、「`D:\ObsidianVault\TechNotes\react-hooks` を元に Zenn 記事を書いて」のように頼みます。外部パスは読み取り専用として扱われ、スキルがそこへ書き込むことはありません。Vault全体のように資料が大きい場合は、ファイル・サブフォルダ・タグで範囲を指定すると、記事に関係のないノートまで読み込まずに済みます。

どちらの場合も、スキルがプラットフォーム・文体・記事構成を順に確認してくるので、それに答えると完成した記事がこのリポジトリの `articles/{platform}/{slug}.md` に保存されます。`sources/` と `articles/` の中身は `.gitignore` で除外しているため、資料や記事がこのリポジトリにコミットされることはありません。

## プラットフォームごとの書き分け

Qiita と Zenn はどちらも技術記事向けなので、frontmatter付きでコード中心の記事を出力します。Qiita ではタグと `:::note` による補足を使い、Zenn では `topics` / `type` / `emoji` を frontmatter に書いたうえで `:::message` や `:::details` などの独自記法を使います。Note は読み物寄りのプラットフォームなので、frontmatterを付けずにコードを控えめにして文章中心で書き、ハッシュタグは本文の末尾に並べます。
