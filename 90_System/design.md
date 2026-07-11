---
created: 2026-07-12
tags: [system, design]
status: draft
---

# 第二の脳 構築設計書

参考: [Claude Codeで「第二の脳」を作った話](https://qiita.com/yamapiiii/items/cc2450f410b64329d275)

## 方針

記事の3層アーキテクチャ(定時実行 → 判断 → 実行)を踏襲しつつ、段階的に構築する。
自動化基盤は **GitHub Actions** を採用(プライベートリポジトリを作成してプッシュする)。

## Phase 1 — コア(手動運用)

- [x] `99_Archives~` → `99_Archives` にリネーム(タイポ修正)
- [x] `00_Inbox/test` の削除(テストファイル)
- [x] CLAUDE.md(思考パートナー設定)
- [x] コマンド5個を `.claude/commands/` に作成
  - `/inbox-process` — Inbox と `../memos.txt` を整理し、各フォルダへ振り分け
  - `/journal` — 今日のジャーナル作成・追記
  - `/weekly-review` — 未処理Inbox・放置プロジェクトの検出とサマリー生成
  - `/brain-status` — ノート数・最終更新・停滞箇所のダッシュボード
  - `/deep-research` — search-specialist を使った Web + brain の並列深掘り
- [x] GitHub リポジトリへのプッシュ(`vimdiff526-oss/secondbrain` 設定済みだった)

## Phase 2 — エージェントとプロジェクト管理

- [ ] エージェント追加(記事の13体から必要な4体に絞る)
  - `knowledge-explorer` — brain 内の横断検索・関連ノート発見
  - `note-writer` — 整理・要約・frontmatter 付与の実務役
  - `socrates` — 問答で思考を深める壁打ち役
  - `pm-layer` — プロジェクトの停滞検出と次の一手の提案
- [ ] `/new-project`, `/decompose`(ゴール→マイルストーン→タスク分解 + GitHub Issue 作成)
- [ ] `/work`(Issue 実行→完了報告→クローズ)

## Phase 3 — 自律運用(GitHub Actions)

- [ ] `anthropics/claude-code-action` を使用。認証は `claude setup-token` で生成した
      `CLAUDE_CODE_OAUTH_TOKEN` を Secrets に登録(APIキー不使用の現運用と一致)
- [ ] ワークフロー
  - 毎朝 6:00 JST — `/inbox-process` 実行、結果をコミット
  - 毎週金曜 18:00 JST — `/weekly-review` 生成
- [ ] ガードレール: 1実行あたり最大10ノート、削除禁止、エラー時は Issue を立てて停止

## 記事から採用しないもの(理由)

- 13体全エージェント → 使途が重複するため4体に圧縮。必要になったら追加
- `/orchestrate`(Issueチェーン自動実行) → 自律性レベル3到達後に検討
- `/voice-note`, `/meeting` → 音声入力の運用が始まったら追加

## 未解決の論点

- 破損した `../CLAUDE.md`(1GB のバイナリ)の扱い — 削除推奨、未対応
- GitHub リポジトリ名・公開範囲(プライベート前提)
- memos.txt を Inbox 取り込み後にクリアするか、残すか
