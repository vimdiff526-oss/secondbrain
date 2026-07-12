---
created: 2026-07-12
tags: [n8n, docker, docker-compose, automation]
status: active
---

# n8nをDocker Composeで起動する最小構成

[SNS投稿自動化](../20_Projects/SNS投稿自動化/README.md) MS1のための調査。TLS終端やドメインが不要な**ローカル/検証用の最小構成**をまとめる(本番向けのTraefik+SSL構成は今回のスコープ外)。

## 出典

- [n8n公式ドキュメント: Docker Compose(Cloud Provider向け本番構成)](https://docs.n8n.io/deploy/host-n8n/install-options/use-a-cloud-provider/use-docker-compose)
- [n8nio/n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)
- 公式のn8n-hostingリポジトリ(GitHub、`docker-compose/withPostgres/`等)にも構成例あり

公式の本番向けdocker-compose例はTraefikによるTLS終端・独自ドメインを前提としており、ローカル検証には過剰。以下は本番例からTraefik・SSL関連設定を除いた、ブラウザで管理画面にアクセスできることを確認するための最小構成。

## compose.yml(最小構成)

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    restart: unless-stopped
    ports:
      - "127.0.0.1:5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - GENERIC_TIMEZONE=Asia/Tokyo
      - TZ=Asia/Tokyo
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

## 起動確認手順

```bash
docker compose up -d
# ブラウザで http://localhost:5678 にアクセスし、初回セットアップ画面が出れば成功
```

更新時:

```bash
docker compose pull
docker compose down
docker compose up -d
```

## ポイント

- **データ永続化**: コンテナは使い捨て前提。ワークフロー・認証情報・設定は`n8n_data`ボリューム(`/home/node/.n8n`)に保存されるため、これがないと再起動時に全て消える
- **ポート公開範囲**: `127.0.0.1:5678:5678`とすることでローカルホストのみに公開し、外部からのアクセスを防ぐ(検証段階での最小権限)
- **イメージ名**: 公式は`docker.n8n.io/n8nio/n8n`(旧`n8nio/n8n`のDocker Hub版もエイリアスとして存在)
- **アーキテクチャ**: n8n公式イメージはarm64にも対応しており、Raspberry Pi環境([StockWatch](../20_Projects/StockWatch/運用状況.md)と同じホスト)でもそのまま起動できる見込み(未検証)
- **本番構成との差分**: 本番ではTraefikでTLS終端・独自ドメイン・セキュリティヘッダ(`N8N_RESTRICT_FILE_ACCESS_TO`等)を追加するが、MS1の「管理画面にアクセスできることを確認する」段階では不要と判断し省略

## 次のタスクへの接続

- 次の調査タスク: 選定したBlueskyの投稿用API認証方式(App Password取得手順)
- その次の実装タスク: 本構成でn8nコンテナを起動し、`http://localhost:5678`にブラウザでアクセスできることを確認する
