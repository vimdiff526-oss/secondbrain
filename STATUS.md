---
created: 2026-07-19
tags: [system, status, dashboard]
status: active
---

# いまの状態

**最終更新: 2026-07-19(Claude)** — このファイルが全体状況の唯一の見る場所。各コマンド・作業の最後に必ず更新される。

## 🟢 稼働中のサービス

| サービス | 場所 | 役割 |
|---|---|---|
| StockWatch | http://192.168.10.142:8000 | 株シグナル監視。毎朝5:00自動収集 |
| Brain Office | http://192.168.10.142:8080 | この司令室(メモ投函・コマンド実行) |
| n8n | http://192.168.10.142:5678 | SNS投稿フロー(2026-07-19起動) |
| GitHub Actions | クラウド | 毎朝6:00 Inbox整理 / 毎週金曜18:00 週次レビュー(2026-07-19稼働開始) |

## 🙋 あなた待ち(上から順に)

1. **n8nの初回セットアップ** — http://192.168.10.142:5678 を開いてオーナーアカウント作成(5分)
2. 破損した `../CLAUDE.md`(1GB)を削除するか判断
3. memos.txt のクリア方針を決める(「クリアしない」で確定なら一言でOK)

全リストと詳細: [90_System/user-actions.md](90_System/user-actions.md)

## 🤖 Claudeの次の一手

- `/work` → sns-writerエージェントの実装([仕様書](20_Projects/SNS投稿自動化/sns-writer仕様.md)は起草済み)

## 📁 プロジェクト

| プロジェクト | 状態 | 次のタスク |
|---|---|---|
| [SNS投稿自動化](20_Projects/SNS投稿自動化/tasks.md) | **進行中**(MS1残り1・MS2着手済み) | Webhook→Bluesky最小ワークフロー作成〔ユーザー〕/ sns-writer実装〔Claude〕 |
| [StockWatch](20_Projects/StockWatch/運用状況.md) | 検証運用中(常駐稼働) | tasks.md未作成。深掘りするなら /decompose |
| 中国輸入ビジネス | 凍結(2026-07-19 → 99_Archives) | — |
| サーフィンサイト | 凍結(2026-07-19 → 99_Archives) | — |

## 📝 最近の動き(直近3件)

- 2026-07-19: システム簡素化 — STATUS.md新設・停止中2プロジェクト凍結・Brain Officeにステータス表示追加
- 2026-07-19: n8n起動完了(Docker導入含む)、LAN公開。GitHub Actions稼働開始
- 2026-07-19: sns-writer仕様起草、実行主体タグ〔Claude〕/〔ユーザー〕導入
