---
title: "すべてのDockerイメージを削除する"
emoji: "🍣"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["docker"]
published: false
---

## 要約

すべてのコンテナを停止し、すべてのコンテナを削除し、すべてのイメージを削除する。

```bash
docker stop $(sudo docker ps -aq)
docker rm $(sudo docker ps -aq)
docker rmi $(sudo docker images -q)
```

## 注意点

必要に応じて`sudo`が必要になる場合もある。

```bash
sudo docker stop $(sudo docker ps -aq)
sudo docker rm $(sudo docker ps -aq)
sudo docker rmi $(sudo docker images -q)
```
