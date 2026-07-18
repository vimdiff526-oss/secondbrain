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

## Phase 2 — エージェントとプロジェクト管理(2026-07-12 構築済み)

- [x] エージェント追加(記事の13体から必要な4体に絞る)
  - `knowledge-explorer` — brain 内の横断検索・関連ノート発見
  - `note-writer` — 整理・要約・frontmatter 付与の実務役
  - `socrates` — 問答で思考を深める壁打ち役(対話専用)
  - `pm-layer` — プロジェクトの停滞検出と次の一手の提案
- [x] `/new-project`, `/decompose`(ゴール→マイルストーン→タスク分解)
- [x] `/work`(次の1タスク実行→tasks.md更新→作業ログ追記)
- **設計変更**: タスク管理は GitHub Issues ではなく各プロジェクトの `tasks.md` を正とする
  (gh CLI 未導入・オフラインでも動くため。Issue連携は必要になったら追加)

## Phase 3 — 自律運用(GitHub Actions)(2026-07-12 構築済み)

- [x] `anthropics/claude-code-action@v1` を使用。認証は `claude setup-token` で生成した
      `CLAUDE_CODE_OAUTH_TOKEN` を Secrets に登録(APIキー不使用の現運用と一致)
- [x] ワークフロー(`.github/workflows/`)
  - `inbox.yml` — 毎朝 6:00 JST に `/inbox-process`(Actions上では memos.txt 取り込みをスキップ)
  - `weekly-review.yml` — 毎週金曜 18:00 JST に `/weekly-review`
- [x] 同期ルール: 全コマンドが作業前 `git pull --rebase`・コミット後 `git push`(CLAUDE.md に記載)
- [x] ガードレール: 1実行あたり最大10ノート、削除禁止、失敗時はワークフローがエラー終了(GitHubから通知メール)
- [x] **要ユーザー作業**: `claude setup-token` の実行と、リポジトリ Secrets への `CLAUDE_CODE_OAUTH_TOKEN` 登録
      (**2026-07-19 完了** — inbox.yml の手動起動が成功し、レベル3の実稼働を確認)
  - 稼働確認の経緯: 2026-07-19時点で brain-bot のコミットが0件 → トークン未登録により7日間空振りと判明。
    Secrets登録後も2つの追加対応が必要だった(今後の同種設定の参考):
    1. ワークフローの `permissions` に `id-token: write` が必須(claude-code-action の OIDC トークン交換)
    2. Claude Code GitHub App(https://github.com/apps/claude)をリポジトリにインストールする必要あり
  - 補助ツール: gh CLI v2.96.0 を `~/.local/bin/gh` に導入済み(未認証。Secrets登録・手動起動はWeb UIで代替した)

## 記事から採用しないもの(理由)

- 13体全エージェント → 使途が重複するため4体に圧縮。必要になったら追加
- `/orchestrate`(Issueチェーン自動実行) → 自律性レベル3到達後に検討
- `/voice-note`, `/meeting` → 音声入力の運用が始まったら追加

## 未解決の論点

**2026-07-19: ユーザー判断が必要な項目は [user-actions.md](./user-actions.md)(要ユーザー作業キュー)へ移管した。以後、新規の要ユーザー作業はキューに直接登録する。**

- 破損した `../CLAUDE.md`(1GB のバイナリ)の扱い — 削除推奨、未対応 → キューへ移管
- GitHub リポジトリ名・公開範囲(プライベート前提)
- memos.txt を Inbox 取り込み後にクリアするか、残すか → キューへ移管

## メモ(2026-07-12 Inbox より統合)

「第二の脳システムを完成させる」という一行メモを Inbox から統合。内容は本設計書のゴールそのものであり、独立した具体的タスクを含まないため、新規プロジェクト化はせず Phase 2/3 の未完了項目(上記チェックボックス)を引き続き進めることで対応する。元ノートは `99_Archives/` へ移動済み。
