# minatoproject

自宅サーバーでホストするDockerコンテナ群。

## Description

``docker-compose.yml`` は以下のアプリケーションを起動します。

- [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/)
- [jrcs/letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/)
- [gitlab/gitlab-ce](https://hub.docker.com/r/gitlab/gitlab-ce/)
- [wordpress](https://hub.docker.com/_/wordpress/)
- [mysql](https://hub.docker.com/_/mysql/)

## Requirement

- CentOS Linux 7.9.2009
- Docker 20.10.6
- docker-compose 1.24.0.rc1

## Usage

### 前提条件

各コンテナはビルドしようとしているサーバのホスト名を解決できる必要があります。

### ネットワーク構成

以下のDockerネットワークを追加します。

```
$ docker network create ssl_proxy
```

### コンテナ構成

このリポジトリをフォークしてクローンします。

```
$ git clone git@github.com:yourname/minatoproject.git
```

クローンしたリポジトリ直下に ``.env`` ファイルを作成し、以下の設定値を記載します。

| VAR                   | DESCRIPTION               |
|:--------------------- |:------------------------- |
| GITLAB_HOSTNAME       | GitLabのドメイン名        |
| WORDPRESS_HOSTNAME    | WordPressのドメイン名     |
| WORDPRESS_DB_MYSQL    | ``wordpress`` を設定      |
| WORDPRESS_DB_USERNAME | WordPressのDBのユーザ名   |
| WORDPRESS_DB_PASSWORD | WordPressのDBのパスワード |
| ADMIN_ADDRESS         | 管理者のメールアドレス    |

<!-- JupyterHubでOAuth認証を使っていたときの記述
In this configuration, you can run JupyterHub and use OAuth with GitLab also built by this configuration. In short, you can from authentication to deproy at all by on-premise.

Therefore, you can't get ``OAUTH_CLIENT_ID`` and ``OAUTH_CLIENT_SECRET`` at first before you build this. So you can set any values both of this. (Persistence of GitLab container is required, conversely)
-->

コンテナを起動します。

```
$ docker-compose up -d
```

全てのコンテナが正常に起動しているか確認します（GitLabの起動には時間がかかります）

```
$ docker-compose ps
      Name                     Command                  State
------------------------------------------------------------------
GitLab              /assets/wrapper                  Up (healthy) 
Proxy               /app/docker-entrypoint.sh  ...   Up
letsencrypt-nginx   /bin/bash /app/entrypoint. ...   Up
WordPress           docker-entrypoint.sh apach ...   Up
WordPressDB         docker-entrypoint.sh mysqld      Up
```

<!-- JupyterHubでOAuth認証を使っていたときの記述
### Configure OAuth

Access GitLab with Web Browser, and login as root.

Check the menu bar, go to "Admin area" (Spanner icon) -> "Applications" -> "New application" for configuration of JupyterHub authentication.

| ITEM         | VALUE                                   |
|:------------ |:--------------------------------------- |
| Name         | (Any)                                   |
| Redirect URI | Value you set at ``OAUTH_CALLBACK_URL`` |
| Truested     | Not check (recommended)                 |
| Scopes       | Check "api"                             |

Click "Submit" and done registration.

After that, you get "Application Id" and "Secret" chars. Set "Application Id" in ``OAUTH_CLIENT_ID`` and "Secret" in ``OAUTH_CLIENT_SECRET`` of ``.env`` file.

Now you can login to JupyterHub with

```
$ docker-compose down
$ docker-compose up -d
```

-->

### Notes

- データ永続化が必要な場合、ホストのディレクトリの権限を ``chmod 777 -R /hoge/directory`` のように変更する必要があることがあります
- 特定の状況下ではGitLabはメモリリソースを莫大に消費することがあります。通常時では1～2GB程度の消費です
- WordPressのマイグレーション中にタイムアウトが発生する場合はProxyコンテナ内の ``etc/nginx/nginx.conf`` の設定を見直してみてください


## Contribution

1. このリポジトリをフォークします
2. 修正ブランチを切ります
3. 変更をコミットします
4. ブランチをプッシュします
5. プルリクエストを作成します

## License

No license

## Author

[minato](https://blog.minatoproject.com/)

## Referance

- [jupyterhub/oauthenticator: OAuth + JupyterHub Authenticator = OAuthenticator](https://github.com/jupyterhub/oauthenticator)
- [GitLabのメモリ使用量を約2GB抑えて安定稼働させるために行ったメモリチューニングの方法 - Qiita](https://qiita.com/k_nakayama/items/9f083a4700915d02104a)
- [Nginxリバースプロキシのタイムアウト対策 - Qiita](https://qiita.com/smallpalace/items/4c0402b03bb3feaf2240)
