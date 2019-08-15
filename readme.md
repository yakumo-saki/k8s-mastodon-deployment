## kubernetes Mastodon (glitch-edition) 

kubernetes用deployment定義です。
`env.production` をご自分のインスタンスの `.env.production` ファイルと置換してお使い下さい。
ここに置いてある `env.production` はサンプルファイルそのままです。

### 前提条件

* `kustomize` https://github.com/kubernetes-sigs/kustomize
* PVCによるPVの割り当てが可能であること

## 書き換える場所

`myinstance` ディレクトリ以下のファイルを書き換えて下さい。
なお、このディレクトリはリネームしても構いません。

### `kustomization.yaml`

以下の場所を好みに応じて書き換えて下さい。

```
namePrefix: yourinstance-   # k8sの各リソースのプレフィックス
commonLabels:
  app: yourinstance         # k8sの各リソースのプレフィックスにつけたいラベル
resources:
  - ../base
configMapGenerator:
  - name: config
    env: env.production
    files:
    literals:
```

### `db-statefulset.yaml`

以下の場所を好みに応じて書き換えて下さい。

```
env:
- name: POSTGRES_INITDB_ARGS
	value: --encoding=UTF-8
- name: POSTGRES_PASSWORD
	value: root            # postgresqlのrootユーザーのパスワードです
- name: POSTGRES_USER
	value: root
```

### dockerコンテナイメージとタグ

`sidekiq-deployment.yaml` `streaming-deployment.yaml` `web-deployment.yaml` の3ファイルにdockerコンテナのイメージ名が含まれています。  
初期値は、https://hub.docker.com/r/tootsuite/mastodon/ を指定してあります。  
バージョンを確認の上、必要であれば変更して下さい。

Glitch Edition の yakumosaki/glitch-soc を使うことも可能です。

### env.production

以下の2項目は注意が必要です。

```
REDIS_HOST=redis
DB_HOST=db
```

`kustomization.yaml` の `namePrefix: yourinstance-` の場合であれば

```
REDIS_HOST=yourinstance-redis
DB_HOST=yourinstance-db
```

となります。

## 蛇足

### 複数インスタンスをお持ちの場合

本deployment定義は、複数インスタンスを作成できるように定義を作成しています。
複数インスタンスを作成する場合は、`myinstance` ディレクトリを好みの名前で
コピーして、そのディレクトリで構築作業を行って下さい。
