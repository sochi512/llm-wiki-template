# LLM Wiki Vault テンプレート

Claude Codeを使って、LLMが継続的に維持する知識ベースをObsidian Vaultで構築するためのテンプレートです。

毎回ファイルをチャットにアップロードする代わりに、LLMがwikiを段階的に構築・維持するパターンです。クロスリファレンスの作成、矛盾の解消、引用付きの質問回答が可能になります。

## クイックスタート

1. このリポジトリをクローンし、フォルダをObsidian Vaultとして開く
2. Vaultルートでターミナルを開き、Claude Codeを起動する：
   ```bash
   claude
   ```
3. 初期化コマンドでwiki構造を作成する：
   ```
   /wiki-init
   ```
4. `raw/` にソースファイルを追加してインジェストする：
   ```
   /wiki-ingest raw/your-article.md
   ```

## コマンド

| コマンド | 使い方 | 説明 |
|---------|-------|-------------|
| `/wiki-init` | `/wiki-init` | wikiのフォルダ構造をセットアップ |
| `/wiki-ingest` | `/wiki-ingest raw/article.md` | ソースファイルをインジェスト。引数を省略すると `raw/` 内の未処理ファイルをすべてインジェスト |
| `/wiki-query` | `/wiki-query RAGとは何か？` | wikiに質問して回答を保存 |
| `/wiki-lint` | `/wiki-lint` | ヘルスチェック — 矛盾・孤立ページ・リンク切れを検出 |

## 構造

```
Vault/
├── CLAUDE.md              wikiメンテナー指示書
├── raw/                   ソースファイル（記事・論文・メモ）— LLMは読み取り専用
├── wiki/
│   ├── index.md           ページ索引 — クエリのエントリーポイント
│   ├── log.md             操作ログ
│   ├── summaries/         記事ごとのまとめページ
│   ├── concepts/          用語・概念ページ
│   ├── entities/          固有名詞ページ（人物・ツール・企業など）
│   └── queries/           クエリ結果の保存ページ（デフォルトでgitignore）
└── .claude/commands/      スラッシュコマンド定義
```

## Tips

- **Obsidian Web Clipper** — ワンクリックでWebページをMarkdownに変換して `raw/` に保存するブラウザ拡張機能
- **グラフビュー**（`Cmd+G`）— wikiページ間のリンクネットワークを可視化
- **Dataviewプラグイン** — YAMLフロントマターから動的テーブルを生成（例：「今週インジェストしたページ一覧」）
- **git** — wikiページは素のMarkdownなので、バージョン管理が無料でついてくる

## クレジット

LLMを使ってセッションごとにゼロから知識を再発見するのではなく、永続的なwikiを段階的に構築・維持するというコアアイデアは [@tobi](https://github.com/tobi) によるものです。

- オリジナルのコンセプトと実装: [github.com/tobi/qmd](https://github.com/tobi/qmd)

このリポジトリはそのパターンをObsidian + Claude Code向けにアレンジしたものです。
