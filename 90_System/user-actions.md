---
created: 2026-07-19
tags: [system, user-actions, queue]
status: active
---

# 要ユーザー作業キュー

Claude が実行できない作業(実機操作・対話認証・意思決定)を1箇所に集約する待ち行列。
**人間側のボトルネックを可視化する**のが目的。散在していると誰も気づかない。

## 運用ルール

- `/work` や `/inbox-process` がブロッカーとして要ユーザー作業を見つけたら、ここに1行追加する
- `/weekly-review` は本キューの滞留(登録から7日以上)を必ず報告する
- 完了したら行は消さず、状態を「完了(日付)」に変えて出典側(tasks.md等)にも反映する
- 書式: `- [ ] <内容> | 登録日 | 出典 | <補足>`

## キュー

- [ ] n8nのブラウザ初回セットアップ(オーナーアカウント作成) | 2026-07-19 | [SNS投稿自動化/tasks.md](../20_Projects/SNS投稿自動化/tasks.md) MS1→次タスクの前提 | `ssh -L 5678:localhost:5678 sloasea@192.168.10.142` でポートフォワードし `http://localhost:5678` を開く。5分作業
- [ ] 破損した `../CLAUDE.md`(1GBのバイナリ)を削除するか判断する | 2026-07-12 | [design.md](./design.md) 未解決の論点 | brain外のためClaudeの削除禁止ルール対象外だが、ユーザー判断が必要。削除推奨
- [ ] memos.txt を Inbox 取り込み後にクリアするか方針を決める | 2026-07-12 | [design.md](./design.md) 未解決の論点 | 現状は残す運用(取り込み済み項番を memos-imported.md で管理)。現運用で問題なければ「クリアしない」で確定してよい

## 完了

- [x] Dockerのインストール | 2026-07-19 | n8n起動の前提 | 完了(2026-07-19)。ユーザーのsudo一時許可によりClaudeが代行。Docker 29.6.2、dockerグループ追加済み
- [x] n8nコンテナを起動し管理画面にアクセス確認する | 2026-07-17 | [SNS投稿自動化/tasks.md](../20_Projects/SNS投稿自動化/tasks.md) MS1 | 完了(2026-07-19)。Claudeが代行、HTTP 200確認。詳細は[実装メモ.md](../20_Projects/SNS投稿自動化/実装メモ.md)
- [x] `claude setup-token` 実行と Secrets への `CLAUDE_CODE_OAUTH_TOKEN` 登録 | 2026-07-12 | [design.md](./design.md) Phase 3 | 完了(2026-07-19)。GitHub App インストールと id-token 権限追加も実施し、Actions 稼働確認済み
- [x] StockWatch の systemd 常駐化: `sudo systemctl enable --now stockwatch` | 2026-05-24 | [StockWatch/運用状況.md](../20_Projects/StockWatch/運用状況.md) | 完了確認(2026-07-19)。実機確認で `stockwatch.service` は enabled かつ active だった(運用状況.mdの「未対応」は古い記載。実行日は未記録)
