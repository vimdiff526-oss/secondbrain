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

## 作業ログ

- 2026-07-12: 投稿先SNSをBlueskyに決定(無料API・審査不要な認証方式を優先)。詳細は [投稿先SNS選定.md](./投稿先SNS選定.md)
- 2026-07-12: n8nのDocker Compose最小構成を調査。TLS・独自ドメイン不要のローカル検証用構成として整理。成果物: [30_Tech_Notes/n8n_Docker_Compose最小構成.md](../../30_Tech_Notes/n8n_Docker_Compose最小構成.md)
- 2026-07-12: BlueskyのAPI認証方式(App Password取得手順、createSession/createRecordの呼び出し方)を調査。成果物: [30_Tech_Notes/Bluesky_API認証方式.md](../../30_Tech_Notes/Bluesky_API認証方式.md)
- 2026-07-17: MS1の次タスク「n8nコンテナ起動・管理画面アクセス確認」はDocker起動を伴う実機作業(brain外操作)のため、実装メモを作成し「実装待ち」(`- [→]`)に変更。成果物: [実装メモ.md](./実装メモ.md)
- 2026-07-19: MS1が〔ユーザー〕タスクで停止中のため、実行主体タグの新ルールに従いMS2へ先行着手。sns-writerの役割・入出力仕様を起草。成果物: [sns-writer仕様.md](./sns-writer仕様.md)
- 2026-07-19: ユーザーのsudo一時許可により、Dockerインストール(29.6.2)とn8nコンテナ起動を代行実行。HTTP 200で管理画面応答を確認し、7/17から停止していたMS1実装タスクが完了。詳細は [実装メモ.md](./実装メモ.md) の実行結果
