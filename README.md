# minatoproject

Docker files for running of my own server.

## Description

The ''docker-compose.yml'' runs applications such as

- GitLab
- Redmine
- JupyterHub
- WordPress
- Jenkins

## Requirement

### Physical Configrations

| ITEM           | VALUE                 |
|:-------------- |:--------------------- |
| OS             | CentOS Linux 7.5.1804 |
| Docker         | 1.13.1                |
| docker-compose | 1.22.0-rc1            |

### Docker Containers

- [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/)
- [jrcs/letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/)
- [gitlab/gitlab-ce](https://hub.docker.com/r/gitlab/gitlab-ce/)
- [wordpress](https://hub.docker.com/_/wordpress/)
- [mysql](https://hub.docker.com/_/mysql/)

## Usage

### Prerequisite

Clients are enable to resolve name of the server you're going to build at first.

### Configure Networks

Add following two docker networks.

```
$ docker network create ssl_proxy
```

### Configure Containers

Fork and clone this repository.

```
$ git clone https://github.com/[YOUR_USER_NAME]/minatoproject
```

Create ``.env`` file in cloned directory, and write below.

| VAR                   | DESCRIPTION                            |
|:--------------------- |:-------------------------------------- |
| GITLAB_HOSTNAME       | Domain name of GitLab                  |
| OAUTH_CLIENT_ID       | Application ID for OAuth of JupyterHub |
| OAUTH_CLIENT_SECRET   | Secret for OAuth of JupyterHub         |
| OAUTH_CALLBACK_URL    | Callback URL of JupyterHub OAuth       |
| WORDPRESS_HOSTNAME    | Domain name of WordPress               |
| WORDPRESS_DB_MYSQL    | Set ``wordpress``                      |
| WORDPRESS_DB_USERNAME | User name of WordPress DB              |
| WORDPRESS_DB_PASSWORD | Password of WordPress DB               |
| ADMIN_ADDRESS         | Your e-mail address                    |

In this configuration, you can run JupyterHub and use OAuth with GitLab also built by this configuration. In short, you can from authentication to deproy at all by on-premise.

Therefore, you can't get ``OAUTH_CLIENT_ID`` and ``OAUTH_CLIENT_SECRET`` at first before you build this. So you can set any values both of this. (Persistence of GitLab container is required, conversely)

Launch containers.

```
$ docker-compose up -d
```

Check if all of container running healthy. It takes a few minutes to launch GitLab.

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

### Notes

- Depending on the status of directories of host you want to data-persistence, you have to set like ``chmod 777 -R /hoge/directory`` .
- Under certain conditions, GitLab uses massively amount of RAM resource. It uses about 1GB - 2GB normally.
- If you get timeout while migration of WordPress and all that, check ``/etc/nginx/nginx.conf`` of Proxy container.

## Install

No installation required.

## Contribution

Fork this repository and clone yours.

Please give me pull requests any time!!

## Author

[minato](https://blog.minatoproject.com/)

## Referance (Japanese)

- [jupyterhub/oauthenticator: OAuth + JupyterHub Authenticator = OAuthenticator](https://github.com/jupyterhub/oauthenticator)
- [GitLabのメモリ使用量を約2GB抑えて安定稼働させるために行ったメモリチューニングの方法 - Qiita](https://qiita.com/k_nakayama/items/9f083a4700915d02104a)
- [Nginxリバースプロキシのタイムアウト対策 - Qiita](https://qiita.com/smallpalace/items/4c0402b03bb3feaf2240)
