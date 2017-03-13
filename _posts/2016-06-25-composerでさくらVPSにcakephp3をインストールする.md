---
layout: post
title: "composerでさくらVPSにcakephp3をインストールしてみる"
date: 2016-06-23
categories: 
- Linux
- cakephp
published: true
---

あらかじめcomposerをインストールしておく。  
また、インストールには、php5.6以上と、`intl` エクステンションが必要になる。  


## とりあえずインストールしてみる

	$ mkdir /var/www/vhost/test-cakephp3.com/
	$ cd /var/www/vhost/test-cakephp3.com/
	$ composer self-update && composer create-project --prefer-dist cakephp/app testcakephp3com

	# 案の定こける
	Problem 1
	    - cakephp/cakephp 3.2.9 requires ext-intl * -> the requested PHP extension intl is missing from your system.
		...
	  To enable extensions, verify that they are enabled in those .ini files:
	    - /etc/php.ini
	    ...
	  You can also run `php --ini` inside terminal to see which files are used by PHP in CLI mode.


## peclでintlをインストールする

	# peclでインストールする
	$ pecl install intl
	
大量のphpエラーがでるので `php-pear` をアップデート

	# remiからphp-pearをアップデート
	$ yum update php-pear --enablerepo=remi

再度インストール

	downloading intl-3.0.0.tgz ...
	Starting to download intl-3.0.0.tgz (248,200 bytes)
	.....done: 248,200 bytes
	150 source files, building
	running: phpize
	Can't find PHP headers in /usr/include/php
	The php-devel package is required for use of this command.
	ERROR: `phpize' failed

`php-devel` が要求されるのでインストールする

	# remi-php56 からインストールする
	$ yum info php-devel --enablerepo=remi-php56
	$ yum install php-devel --enablerepo=remi-php56

	# php-pecl-jsonc-devel もインストールされる
	インストール:
	  php-devel.x86_64 0:5.6.23-1.el6.remi                                                                                                                        

	依存性関連をインストールしました:
	  php-pecl-jsonc-devel.x86_64 0:1.3.10-1.el6.remi.5.6    	

再々度インストール

	$ pecl install intl
	Specify where ICU libraries and headers can be found [DEFAULT] : 
	checking for icu-config... no
	checking for location of ICU headers and libraries... not found
	configure: error: Unable to detect ICU prefix or no failed. Please verify ICU install prefix and make sure icu-config works.
	ERROR: `/var/tmp/intl/configure --with-php-config=/usr/bin/php-config --with-icu-dir=DEFAULT' failed

`icu` をインストール

	$ yum install icu

	# libicu もインストールされる
	インストール:
	  icu.x86_64 0:4.2.1-14.el6

	依存性関連をインストールしました:
	  libicu.x86_64 0:4.2.1-14.el6  

`libicu-devel` もインストールする

	$ yum install libicu-devel


再再再度インストール

	$ pecl install intl

	# 今度はうまくいった
	Build process completed successfully
	Installing '/usr/lib64/php/modules/intl.so'
	install ok: channel://pecl.php.net/intl-3.0.0
	configuration option "php_ini" is not set to php.ini location
	You should add "extension=intl.so" to php.ini

`php.ini` に設定を追加する

	$ vi /etc/php.ini

下記を追記

	extension=intl.so


## 再度composer

	$ composer self-update && composer create-project --prefer-dist cakephp/app testcakephp3com

今度は成功  

	# パーミッションを設定するか聞かれるのでy
	Set Folder Permissions ? (Default to Y) [Y,n]? y
	Updated Security.salt value in config/app.php

セキュリティsaltの設定はあとでやるとして、ローカルサーバーを立ち上げて確認してみる

	# デフォルトはlocalhostで立ち上がるので、hostを指定して起動
	$ testcakephp3com/bin/cake server -H 153.121.53.203

ブラウザで http://153.121.53.203:8765/ にアクセス	

	Get the Ovens Ready

が表示されたら成功！


## 参考
[CakePHP3.0をインストールしてみる(自分用めもめも - Qiita](http://qiita.com/ysnsyks2/items/176cfddbdf1f79d65a75)  
[春だし CakePHP 3.0をComposer経由でCentOS6.5にインストール してみた - Qiita](http://qiita.com/IKEA_dless/items/f536b3c009af295bb4a5)  
[インストール](http://book.cakephp.org/3.0/ja/installation.html)  


