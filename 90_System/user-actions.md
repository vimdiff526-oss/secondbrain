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

- [ ] n8nコンテナを起動し管理画面にアクセス確認する | 2026-07-17 | [SNS投稿自動化/tasks.md](../20_Projects/SNS投稿自動化/tasks.md) MS1 | 手順は[実装メモ.md](../20_Projects/SNS投稿自動化/実装メモ.md)に完備。compose.yml配置→`docker compose up -d`→ブラウザ確認の約15分作業
- [ ] StockWatch の systemd 常駐化: `sudo systemctl enable --now stockwatch` を実行する | 2026-05-24 | [StockWatch/運用状況.md](../20_Projects/StockWatch/運用状況.md) | 1コマンド。未実行なら再起動のたびに手動起動が必要な状態
- [ ] 破損した `../CLAUDE.md`(1GBのバイナリ)を削除するか判断する | 2026-07-12 | [design.md](./design.md) 未解決の論点 | brain外のためClaudeの削除禁止ルール対象外だが、ユーザー判断が必要。削除推奨
- [ ] memos.txt を Inbox 取り込み後にクリアするか方針を決める | 2026-07-12 | [design.md](./design.md) 未解決の論点 | 現状は残す運用(取り込み済み項番を memos-imported.md で管理)。現運用で問題なければ「クリアしない」で確定してよい

## 完了

- [x] `claude setup-token` 実行と Secrets への `CLAUDE_CODE_OAUTH_TOKEN` 登録 | 2026-07-12 | [design.md](./design.md) Phase 3 | 完了(2026-07-19)。GitHub App インストールと id-token 権限追加も実施し、Actions 稼働確認済み
