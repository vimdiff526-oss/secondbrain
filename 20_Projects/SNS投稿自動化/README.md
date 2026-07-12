---
created: 2026-07-12
tags: [sns, n8n, subagent, automation]
status: active
---

# SNS投稿自動化

## 背景

n8n を使ってSNS投稿フローを構築し、サブエージェント経由で投稿を自動化したい。原典は [構想.md](./構想.md)(memos.txt項番002、2026-05-17)。

サブエージェントの役割として想定されているのは以下の2点:

- 投稿文の体裁を整える
- n8n の起動を行う

投稿先SNS・投稿頻度・承認フローなど詳細はまだ未記載(未記録)。

## 完了の定義(ゴール)

投稿したい内容(テキスト)をサブエージェントに渡すと、体裁が整えられたうえで n8n 経由で指定SNSへ自動投稿され、その結果(成功/失敗)が確認できる状態。

## 参考にした既存知見

- `90_System/design.md` — 既存サブエージェント(knowledge-explorer, note-writer, socrates, pm-layer)の設計方針・粒度判断
- `.github/workflows/inbox.yml`, `.github/workflows/weekly-review.yml` — GitHub Actions自動化のガードレール設計(実行上限、失敗時の扱い)
- `20_Projects/StockWatch/運用状況.md` — 定時実行(APScheduler)の実装パターン。n8nの代替検討時の参考

n8n本体・SNS API連携の技術知見はbrain内に未記録のため、MS1で調査から着手する。
