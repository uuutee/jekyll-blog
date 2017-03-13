---
layout: post
title: "composer で pear をインストールする"
date: 2016-06-16
categories: 
- Linux
- cakephp
published: true
---

KAGOYA専用サーバでインストールしてみる。  
	
```sh
	# curlが使えないので wget
	$ wget --no-check-certificate https://getcomposer.org/installer | php

	# リネーム
	$ mv installer composer.phar

	# テスト
	$ php composer.phar

	# 対称ディレクトリに移動
	$ mv composer.phar public_html/uno-mt5-temp10.com/bbs/
	$ cd public_html/uno-mt5-temp10.com/bbs/	

	# composer.json を作成
	$ vi composer.json

	# インストール (カレントにcomposer.pharがないとインストールできない・・)
	$ php composer.phar install
```

エラーになる

	Loading composer repositories with package information
	Initializing PEAR repository http://pear.php.net
	PEAR repository from http://pear.php.net could not be loaded. Your configuration does not allow connections to http://pear.php.net/channel.xml. See https://getcomposer.org/doc/06-config.md#secure-http for details.
	Updating dependencies (including require-dev)
	Your requirements could not be resolved to an installable set of packages.

	  Problem 1
	    - The requested package pear-pear.php.net/pear could not be found in any version, there may be a typo in the package name.

	Potential causes:
	 - A typo in the package name
	 - The package is not available in a stable-enough version according to your minimum-stability setting
	   see <https://getcomposer.org/doc/04-schema.md#minimum-stability> for more details.

	Read <https://getcomposer.org/doc/articles/troubleshooting.md> for further common problems.
	Content-type: text/html

	Warning: Composer should be invoked via the CLI version of PHP, not the cgi-fcgi SAPI

http接続だからエラーになる模様  
`composer.json` を `"secure-http": false` を追記

```json
{
    "config": {
        "secure-http": false
    },
    "repositories": [
		{
			"type": "pear",
			"url": "http://pear.php.net/"
		}
	],
	"require": {
		"pear-pear.php.net/PEAR" : "*"
	}
} 
```

再度インストールしたところ、 `/vender/` にインストールされた


## 参考
[【php】PEARをインストールする（Composerで） at softelメモ](https://www.softel.co.jp/blogs/tech/archives/4057)  
[Composer ドキュメント日本語訳](https://kohkimakimoto.github.io/getcomposer.org_doc_jp/doc/01-basic-usage.html)  
[CentOS6.7にてPEARのライブラリをComposerでインストールしようとしたらはまった - Qiita](http://qiita.com/yousan/items/92748747548b94365dff)  

