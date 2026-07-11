---
description: 今日のジャーナルを作成・追記する
argument-hint: [記録したい内容(省略可)]
---

今日のジャーナル `10_Journal/<今日の日付 YYYY-MM-DD>.md` を作成または追記してください。

## 手順

1. 今日の日付を確認する(`date +%F`)
2. 該当ファイルがなければ frontmatter(`created`, `tags: [journal]`, `status: active`)付きで新規作成する
3. 引数が与えられていればその内容を、時刻見出し(`## HH:MM`)付きで追記する: $ARGUMENTS
4. 引数が空の場合は、今日の brain 内の変更(`git log --since=midnight`)と `00_Inbox/` の新着を要約し、「今日の動き」として追記する
5. 過去のジャーナルや関連ノートに繋がる話題があれば、`関連: <パス>` の形で相互リンクを付ける
6. コミットする(例: `journal: YYYY-MM-DD`)
