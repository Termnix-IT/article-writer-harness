# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## このリポジトリの性質

手元の資料から Qiita / Note / Zenn 向けの記事Markdownを書き起こすための、Claude Code 用ハーネスです。アプリケーションコードはなく、ビルド・lint・テストのコマンドもありません。中身は `.claude/skills/article-writer/` にあるスキル定義（Markdownの指示書）と、入出力用の `sources/`・`articles/` だけです。

## スキルの構成

「記事を書いて」「Qiita用に」などの依頼は `article-writer` スキルで処理します。`SKILL.md` がオーケストレーターで、プラットフォームの確認 → 資料の確認 → `agents/analyzer.md` による資料の解析 → 文体の選択 → 構成案の確認 → `agents/writer-{qiita,note,zenn}.md` による執筆 → `articles/{platform}/{slug}.md` への保存、の順に進みます。文体と構成案はユーザーに選んでもらってから執筆に進む設計です。

スキルとエージェントの指示書はトークン節約のため英語で書き、ユーザーに見せる対話文と記事本文は日本語にしています。指示書を編集するときもこの使い分けを保ってください。

## 入出力の約束

- 入力は `sources/{project-name}/`、または Obsidian Vault などの外部の絶対パスです。外部パスは読み取り専用で、書き込みもハーネスへのコピーもしません。
- 出力は必ず `articles/{platform}/` に置きます。`{slug}` はタイトルのケバブケースで、タイトルが日本語なら `{資料フォルダ名}-{YYYYMMDD}` にします。同名ファイルがあれば `-2` などを付け、上書きしません。
- `sources/` と `articles/` の中身は `.gitignore` で除外しているため、記事の作成・修正だけの作業ではこのリポジトリにcommitするものはありません。commit対象になるのは README、スキル定義、`.gitignore` などハーネス自体の変更です。
- 資料に書かれていない機能や仕様を記事に書き足しません。資料に数値データ（JSONなど）や画像がある場合は、記事中の数値や記述がそれと一致しているかを確認します。

## Qiita への投稿

Qiita 向けの記事は、別リポジトリ qiita-cli-articles（ローカルは `ProjectFolder\WebProject\qiita-cli-articles`）に移して投稿します。移し方は README の「Qiita への投稿（Qiita CLI 連携）」に、プレビュー・公開の切り替え・画像のアップロード・Qiita の画面で記事を直したり削除したりした場合の対処は qiita-cli-articles の README にあります。手順を変えるときは説明が重複しないよう、ハーネス側には移し方だけを残し、それ以外は qiita-cli-articles の README を更新してください（`SKILL.md` の Step 7 も同じ README を参照しています）。

作業時に特に注意する点は次のとおりです。

- qiita-cli-articles の `main` への push で GitHub Actions が `public/` の記事を Qiita に投稿します。`ignorePublish: false` の記事を含む push は一般公開になるため、push の前にユーザーの確認を取ります。手元の `qiita publish` では投稿しません。
- Qiita CLI はローカル画像を扱えないため、記事中の `./xxx.jpg` は Qiita にアップロードした `qiita-image-store` の URL に差し替えてから投稿します。
- Actions の `qiita publish --all` は、どれか1記事でも「内容がQiita上の記事より古い可能性があります」と判断すると全体が失敗し、新しい記事も投稿されません。Qiita の画面で公開済み記事が直されていないかを確認し、直されていればそのファイルを Qiita 上の内容に合わせてから push します。
- 投稿後は Actions が `id` と `updated_at` を書き込んだコミットを push し返すので、次の作業の前に `git pull` します。
