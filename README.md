# XRP Ledger Node (xrpld) - Local Docker Setup

このプロジェクトは、**XRPL（XRP Ledger）ノード**をローカル環境（Mac / Docker Desktop）で簡単に構築・起動するためのセットアップです。
目的は「**XRPLを理解し、DB構造やLedger動作を観察すること**」。

---

## 📁 ディレクトリ構成

```bash
~/xrpl
├─ docker-compose.yml # コンテナ構成
├─ config/
│ └─ rippled.cfg # ノード設定（DB・ポートなど）
└─ data/ # LedgerやDBが保存される永続ディレクトリ
```

---

## 🚀 起動手順

```bash
cd ~/xrpl
docker compose up -d
```

初回起動時は Ledger 同期が行われます。
```bash
docker ps
docker logs -f xrpld
```

## 🔍 ノード情報確認
### サーバ情報
```bash
curl -s -X POST localhost:5005 \
  -H "Content-Type: application/json" \
  -d '{"method":"server_info"}' | jq
```

### Ledger範囲
```bash
curl -s -X POST localhost:5005 \
  -H "Content-Type: application/json" \
  -d '{"method":"ledger_range"}' | jq
```

### Ledger内容（最新）
```bash
curl -s -X POST localhost:5005 \
  -H "Content-Type: application/json" \
  -d '{"method":"ledger"}' | jq
```

## ⚙️ 設定概要（config/rippled.cfg）
- DB種別: NuDB（非圧縮、安定性重視）
- 履歴保持: online_delete = 512（約256件の履歴を維持）
- メモリ設定: [node_size] small（Docker on Macに最適）
- 公開ポート
  - 5005 : JSON-RPC
  - 6006 : WebSocket
  - 51235 : P2P

## 💾 データ保存場所
コンテナ内： /var/lib/rippled
ホスト側： ~/xrpl/data
コンテナを削除しても data/ に Ledger や DB が残るため、再起動後も同期を引き継ぎます。

## 🧰 よく使うコマンド

```bash
# コンテナの状態を確認
docker compose ps

# ログをリアルタイムで見る
docker logs -f xrpld

# 再起動
docker compose restart xrpld

# 停止
docker compose down

# ディスク使用量を確認
du -h ~/xrpl/data
```

## 🧩 環境情報
Image: xrpllabsofficial/xrpld:latest
Platform: macOS + Docker Desktop
DB: NuDB
履歴: 256 ledgers (online_delete=512)
