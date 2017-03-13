---
layout: post
title: "composerでさくらVPSにcakephp2をインストールしてみる"
date: 2016-06-23
categories: 
- Linux
- cakephp
published: true
---

composerをインストール

	# インストールする
	$ curl -sS https://getcomposer.org/installer | php

	# パスが通っている場所に移動
	$ mv composer.phar /usr/local/bin/composer

	# テストする
	$ composer

composerの文字が表示されたらインストール完了。  

`php_pdo.dll` と `php_pdo_mysql.dll` が見つからない旨のエラーが出るので解消する。

	PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/lib64/php/modules/php_pdo.dll' - /usr/lib64/php/modules/php_pdo.dll: cannot open shared object file: No such file or directory in Unknown on line 0
	PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/lib64/php/modules/php_pdo_mysql.dll' - /usr/lib64/php/modules/php_pdo_mysql.dll: cannot open shared object file: No such file or directory in Unknown on line 0

`/etc/php.ini` の最下部に、なぜか dll読み込みの設定が追加されていたので、コメントアウトする。
(なんのために追加していたのか全く思い出せない。。)  

	$ vi /etc/php.ini

	; added 2014-04-30
	;extension=php_pdo.dll
	;extension=php_pdo_mysql.dll

コメントアウトするとエラーが出なくなった。  

composer.json を作成する

	$ vi composer.json

```json
{
	"name": "testcakephp2",
	"repositories": [
		{
			"type": "pear",
			"url": "http://pear.cakephp.org"
		}
	],
	"require": {
		"cakephp/cakephp": ">=2.6.4,<3.0.0"
	},
	"require-dev": {
		"phpunit/phpunit": "3.7.37",
		"cakephp/debug_kit" : ">=2.2.4,<3.0.0"
	},
	"config": {
		"vendor-dir": "vendors/"
	},
	"extra": {
		"installer-paths": {
			"./plugins/{$name}/": [
				"cakephp/debug_kit"
			]
		}
	}
}

```

インストールする
	
	$ composer install
	$ vendors/bin/cake bake project app

/app/ 内にファイルが生成される。  
このままだとアクセスできないので、 virtualhostを設定後、 .htaccess を作成する

	<IfModule mod_rewrite.c>
		RewriteEngine on
		RewriteRule    ^$ app/webroot/    [L]
		RewriteRule    (.*) app/webroot/$1 [L]
	</IfModule>

http://test-cakephp2.com/ にアクセスすると、正常に表示された。  

DebugKitの有効化

`/app/Config/bootstrap.php`

```
	CakePlugin::loadAll();
```

githubでリポジトリつくってclone

	$ git clone https://github.com/uuutee/cakephp2-template.git
	$ cd  
	$ mv cakephp2-template/.git/ ./
	$ mv cakephp2-template/README.md ./
	$ rm -rf cakephp2-template

.gitignore を作成

	$ vi .gitignore

```
/vendors
/plugins

/app/tmp/*
/app/Config/core.php
/app/Config/database.php
```

	$ git add -A
	$ git commit -m "First commit."
	$ git push origin master

git push が forbidden になる  

	error: The requested URL returned error: 403 Forbidden while accessing https://github.com/uuutee/cakephp2-template.git/info/refs

リモートURLにユーザー名を含めたものをセットする  

	$ git remote set-url origin https://uuutee@github.com/uuutee/cakephp2-template.git

正常にpushできた。  

	$ git push origin master


