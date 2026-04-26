# WIP: wiki-ingest 高速化

## ブランチ
`improve-wiki-ingest`

## 完了済みコミット

| コミット | 内容 |
|---------|------|
| `eee8c38` | インジェスト手順を4フェーズ並列化（逐次20〜30ラウンド → 6ラウンド） |
| `212df37` | 処理時間計測を追加（フェーズ0で `date +%s`、log.md に `⏱ Xs` 記録） |

## 未コミットの変更（要コミット）

- `CLAUDE.md` — フェーズ0（date +%s計測）を削除、log.md書式をラウンド数に変更
- `.claude/commands/wiki-ingest.md` — 同様に更新

### 変更内容の詳細
計測方式を `⏱ Xs`（elapsed時間）から `Nrounds (P1:N P2:0 P3:N P4:N)` に変更。
理由: elapsed時間には承認待ち時間が含まれるため、並列化効果の計測に使えない。ラウンド数は承認待ちに左右されない。

### log.mdのフォーマット変更後の例
```
## [2026-04-25] ingest | タイトル | 4rounds (P1:1 P2:0 P3:1 P4:2)
```

## 計測結果（動作確認済み）

| フェーズ | ラウンド | 内容 |
|---------|---------|------|
| P1 | 1 | 4 tool calls並列（raw + concepts + entities + index） |
| P2 | 0 | tool callなし（LLM内部処理のみ） |
| P3 | 1 | 8 tool calls並列（summary + concepts×5 + entities×2） |
| P4 | 2 | index.md → log.md（逐次） |
| **合計** | **4** | |

**ボトルネック**: P4の2ラウンド（index.md更新 → log.md追記が逐次）

## 次のタスク

1. **コミット**: 未コミットの変更（CLAUDE.md + wiki-ingest.md）をコミット
2. **PR作成**: `improve-wiki-ingest` → `main`

## 今後の改善候補（未着手）

- **P4を1ラウンドに削減**: index.mdとlog.mdを並列書き込みにする（現状は順序依存がないので並列化可能）→ 4rounds → 3rounds
- **index.mdにキーポイントを持たせて既存ページ読み込みを不要にする（4→3ラウンド）**
  - 各ページの概要3〜5点を index.md に記載
  - インジェスト時は index.md のみ読んで更新要否を判断
  - 既存ページへの更新は追記のみ（深い統合は lint に委ねる）
