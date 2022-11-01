# Drupal Multi Site Create Environment

## Drupalプロジェクトの作成

ローカル環境で `composer create-project` を実行し、Drupalプロジェクトを作成する。後々drushコマンドを使用してサイトをインストールするのでdrushコマンドもインストールする。

```bash
composer create-project drupal/recommended-project my-multi-site
cd my-multi-site
composer require drush/drush
```

## 各設定ファイルの作成

次の4つのファイルを作成していく。

- .lando.yml
- drush/sites/site1.site.yml
- drush/sites/site2.site.yml
- web/sites/sites.php

### .lnad.yml

.lando.ymlを次の内容で作成する。


``` yml .lando.yml
name: my-multi-site
recipe: drupal9
config:
  webroot: web
proxy:
  appserver:
    - site1.lndo.site
    - site2.lndo.site
services:
  site1_db:
    type: mysql:5.7
    portforward: 33068
  site2_db:
    type: mysql:5.7
    portforward: 33069
```


### Create drush directories

Drushで簡単にマルチサイトを操作できるようにするため、Drushのサイトエイリアスファイルを作成する。まず`drush/sites` ディレクトリを作成する。

```
mkdir -p drush/sites
```

YAMLファイルのrootキーには、Drupalのプロジェクトルートを指定する。今回はlandoで作成した環境に対してdrushコマンドを実行するので、ローカル環境のプロジェクトルートではなく、lando環境内のプロジェクトルートを指定する。

```yml drush/sites/site1.site.yml
local:
  root: /app
  uri: 'http://site1.lndo.site'
  paths:
   - files: sites/site1/files
```


```yml drush/sites/site2.site.yml
local:
  root: /app
  uri: 'http://site2.lndo.site'
  paths:
   - files: sites/site2/files
```

web/sites/sites.phpファイルを作成する。

```php  web/sites/sites.php
<?php
$sites['site1.lndo.site'] = 'site1';
$sites['site2.lndo.site'] = 'site2';
```

web/sites/site1、web/sites/site2ディレクトリを作成する。

```bash
mkdir web/sites/site1
mkdir web/sites/site2
```

各サイトの起動
lando環境を起動する。

```bash
$ lando start

# ...省略...

Here are some vitals:

 NAME            my-multi-site
 LOCATION        /Users/{}/my-multi-site
 SERVICES        appserver, database, site1_db, site2_db
 APPSERVER URLS  https://localhost:54934
                 http://localhost:54935
                 http://site1.lndo.site/
                 https://site1.lndo.site/
                 http://site2.lndo.site/
                 https://site2.lndo.site/
```

これでサイトにアクセスできるので、サイトの手順に沿ってインストールをすすめる。


参考：<https://qiita.com/hmaruyama/items/a01b0b18ec99df33aedf>
