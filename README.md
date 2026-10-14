# NetArticle — 記事執筆ハーネス

手元のコードやメモ、ObsidianのVaultなどを資料として渡すと、Claude Code の `article-writer` スキルが Qiita / Note / Zenn 向けの記事Markdownを書き起こすためのハーネスです。投稿は自動では行わないので、生成されたMarkdownは各プラットフォームに手動で貼り付けて投稿してください。

## 使い方

資料の渡し方は2通りあります。ひとつは、このリポジトリの `sources/{project-name}/` にコード・README・下書きメモ・画像などを置き、Claude Code に「`sources/my-app` から Qiita 記事を書いて」と頼む方法です。もうひとつは、ObsidianのVaultや別の場所にあるプロジェクトを絶対パスで指定する方法で、「`D:\ObsidianVault\TechNotes\react-hooks` を元に Zenn 記事を書いて」のように頼みます。外部パスは読み取り専用として扱われ、スキルがそこへ書き込むことはありません。Vault全体のように資料が大きい場合は、ファイル・サブフォルダ・タグで範囲を指定すると、記事に関係のないノートまで読み込まずに済みます。

どちらの場合も、スキルがプラットフォーム・文体・記事構成を順に確認してくるので、それに答えると完成した記事がこのリポジトリの `articles/{platform}/{slug}.md` に保存されます。`sources/` と `articles/` の中身は `.gitignore` で除外しているため、資料や記事がこのリポジトリにコミットされることはありません。

## Qiita への投稿（Qiita CLI 連携）

Qiita 向けの記事は、Qiita CLI で記事を管理する別リポジトリ [qiita-cli-articles](https://github.com/Termnix-IT/qiita-cli-articles)（private、ローカルは `ProjectFolder\WebProject\qiita-cli-articles`）に移して投稿します。このリポジトリは `main` への push をきっかけに GitHub Actions が `public/` 内の記事を Qiita へ投稿・更新する構成になっているので、手元の CLI から投稿する必要はありません。手元で投稿してから push すると同じ記事を二重に処理することになるため、投稿は push だけで行ってください。

移すときは、qiita-cli-articles で `npm run new -- <ファイル名>` を実行して `public/` にひな形を作り、このハーネスの `articles/qiita/` に出力された記事の本文と `title`・`tags` をそこへ写します。ひな形の frontmatter には Qiita CLI が使う `id` や `ignorePublish` などの項目が含まれているので、ハーネスの出力をそのままコピーせず、ひな形を残したまま中身を入れ替えます。ハーネスの出力では画像が `./xxx.png` のような相対パスのままになっていますが、Qiita CLI はローカルの画像を扱えないため、Qiita にアップロードした画像の URL に差し替えてから投稿します。

プレビュー、`ignorePublish` を使った公開の切り替え、画像のアップロード方法、投稿後の `git pull`、Qiita の画面で記事を直したり削除したりした場合の対処は、qiita-cli-articles の README にまとめています。投稿作業はその README に沿って進めてください。

## プラットフォームごとの書き分け

Qiita と Zenn はどちらも技術記事向けなので、frontmatter付きでコード中心の記事を出力します。Qiita ではタグと `:::note` による補足を使い、Zenn では `topics` / `type` / `emoji` を frontmatter に書いたうえで `:::message` や `:::details` などの独自記法を使います。Note は読み物寄りのプラットフォームなので、frontmatterを付けずにコードを控えめにして文章中心で書き、ハッシュタグは本文の末尾に並べます。
