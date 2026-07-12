---
created: 2026-07-12
tags: [bluesky, at-protocol, xrpc, api, authentication, n8n]
status: active
---

# Bluesky投稿用API認証方式(App Password)

[SNS投稿自動化](../20_Projects/SNS投稿自動化/README.md) MS1のための調査。[投稿先SNS選定.md](../20_Projects/SNS投稿自動化/投稿先SNS選定.md)でBlueskyを選定した理由の一つが「App Password方式で審査不要・シンプル」だった点の裏付け調査。

## 出典

- [Bluesky公式ドキュメント: Get Started](https://docs.bsky.app/docs/get-started)
- [Bluesky公式ドキュメント: Posting via the Bluesky API](https://docs.bsky.app/blog/create-post)
- [Bluesky公式ドキュメント: com.atproto.server.createAppPassword](https://docs.bsky.app/docs/api/com-atproto-server-create-app-password)
- [How to Create a Bluesky App Password: Step-by-Step | TheBlue.social](https://theblue.social/create-bluesky-app-password)

## 1. App Passwordの取得手順(ブラウザ操作)

1. `https://bsky.app/settings/app-passwords` にアクセス(またはSettings → Privacy & Security → App Passwords)
2. 「Add App Password」をクリック
3. 名前を入力(用途がわかるように、例: `n8n-post-automation`)
4. 「Allow access to your direct messages」のチェックボックスは**チェックしない**(投稿のみが目的のため最小権限)
5. 生成された `xxxx-xxxx-xxxx-xxxx` 形式のパスワードをその場でコピーして保管する(**この画面でしか表示されない**)

審査・申請プロセスは不要。X(Twitter)のOAuth申請と異なり即座に発行できる。

## 2. 認証フロー(createSession)

取得したApp Passwordとハンドル(またはDID/メールアドレス)を使い、`com.atproto.server.createSession` にPOSTしてセッショントークンを取得する。

```bash
curl -X POST https://bsky.social/xrpc/com.atproto.server.createSession \
  -H "Content-Type: application/json" \
  -d '{"identifier": "handle.example.com", "password": "xxxx-xxxx-xxxx-xxxx"}'
```

レスポンスに含まれる2種類のトークン:

- `accessJwt` — APIリクエストの認証に使う。**数分で失効**する短命トークン
- `refreshJwt` — `accessJwt` を再発行するための長命トークン

n8nワークフローでは、Webhook起動のたびに`createSession`を呼んで`accessJwt`を都度取得する構成が最もシンプル(トークンのリフレッシュ管理を省略できる)。

## 3. 投稿の作成(createRecord)

取得した`accessJwt`を`Authorization: Bearer`ヘッダーに載せ、`com.atproto.repo.createRecord`に投稿内容をPOSTする。

```bash
curl -X POST https://bsky.social/xrpc/com.atproto.repo.createRecord \
  -H "Authorization: Bearer $ACCESS_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "repo": "handle.example.com",
    "collection": "app.bsky.feed.post",
    "record": {
      "$type": "app.bsky.feed.post",
      "text": "Hello World!",
      "createdAt": "2026-07-12T00:00:00.000Z"
    }
  }'
```

`repo`にはセッション取得時のハンドル(またはDID)を指定する。`createdAt`はISO 8601形式のUTCタイムスタンプ。

## ポイント

- **審査不要・即時発行**: X(Twitter)のOAuth申請と違い、Settings画面から即座にApp Passwordを発行できる。これが選定理由([投稿先SNS選定.md](../20_Projects/SNS投稿自動化/投稿先SNS選定.md))の裏付け
- **公式n8nノードは無い**: HTTP Requestノードで上記2つのXRPCエンドポイント(`createSession`→`createRecord`)を順に呼ぶワークフローになる見込み
- **App Passwordは1度しか表示されない**: n8nの認証情報(Credential)に保存する前提で、発行後すぐに控える必要がある
- **DM権限は付与しない**: 投稿自動化のみが目的のため、App Password発行時に「Allow access to your direct messages」はチェックしないことで権限を最小化する
- **トークンの寿命管理**: `accessJwt`は短命なため、n8nワークフロー内では「Webhook起動 → createSessionで都度認証 → createRecordで投稿」という使い捨て設計にすると`refreshJwt`の管理が不要になり実装がシンプルになる

## 次のタスクへの接続

- 次の実装タスク: [n8n_Docker_Compose最小構成.md](./n8n_Docker_Compose最小構成.md)の構成でn8nコンテナを起動し、管理画面にブラウザでアクセスできることを確認する
- その次の実装タスク: n8nでWebhook起動→上記`createSession`/`createRecord`呼び出しの最小ワークフローを作成し、手動実行でテスト投稿が成功することを確認する
