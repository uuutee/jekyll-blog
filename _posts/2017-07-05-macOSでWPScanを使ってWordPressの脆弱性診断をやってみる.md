---
layout: post
title: "macOSでWPScanを使ってWordPressの脆弱性診断をやってみる"
date: 2017-07-05
categories: WordPress 
published: true
---

## WPScanとは
ruby製のWordPress脆弱性診断ツール。  
脆弱性診断やペネトレーションテスト（侵入試験）を行うことができる。


## WPScanでできること
- 脆弱性のあるプラグインがインストールされていないかのチェック
- 対象のWordPressバージョンに対する脆弱性のチェック
- パスワードリストを用いた管理画面へのブルートフォースアタックのテスト
など


## 注意
WPScanは対象サーバに異常に負荷をかけます。注意の上使用してください。  


## インストール
githubのREADMEを参考にインストールする  
※rubyのバージョン管理は `rbenv` を使用

	$ git clone https://github.com/wpscanteam/wpscan.git
	$ cd wpscan
	$ gem install bundler
	$ bundle install --without test


## インストール時の問題
WPScanに限った話ではないが、インストールに際していくつか問題があったので対処法をメモ

### gem installでruby2.4.0が要求される
`.ruby-version`を確認すると2.4.0が要求されている。  
  
ruby 2.4.0 をインストールする

	$ rbenv install -l | grep 2.4.0
	2.4.0-dev

homebrewの `ruby-build` が2.4.0-devまでしか対応していないので、  
最新のリポジトリから2.4.0をインストールできるようにする  

	$ cd ~/.rbenv/plugins/ruby-build
	$ git pull origin master
	$ rbenv install -v 2.4.0


### bundle install の nokogiriがこける

nokogiri のインストールが失敗する

	An error occurred while installing nokogiri (1.7.1), and Bundler cannot continue.
	Make sure that `gem install nokogiri -v '1.7.1'` succeeds before bundling.

#### ローカルのライブラリが利用できない
ログを確認

	$ less /Users/ut/.rbenv/versions/2.4.0/gemsets/wpscan/extensions/x86_64-darwin-16/2.4.0-static/nokogiri-1.7.1/gem_make.out

    gem install nokogiri -- --use-system-libraries
        [--with-xml2-config=/path/to/xml2-config]
        [--with-xslt-config=/path/to/xslt-config]

	If you are using Bundler, tell it to use the option:

	    bundle config build.nokogiri --use-system-libraries
	    bundle install


bundlerの設定を変更して、システムのライブラリを使用するようにする

	$ bundle config build.nokogiri --use-system-libraries


#### libxml2 が存在しない
まだインストールが失敗するのでログを確認 `libxml2` が存在しない模様

	Building nokogiri using system libraries.
	ERROR: cannot discover where libxml2 is located on your system. please make sure `pkg-config` is installed.


homebrewを確認するとインストールはされている

	$ brew list | grep libxml2
	libxml2

	$ brew info libxml2
	libxml2: stable 2.9.4 (bottled), HEAD [keg-only]
	GNOME XML library
	http://xmlsoft.org
	/usr/local/Cellar/libxml2/2.9.4 (276 files, 9.8MB)
	  Poured from bottle on 2016-12-22 at 00:29:44
	From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/libxml2.rb
	==> Caveats
	This formula is keg-only, which means it was not symlinked into /usr/local,
	because macOS already provides this software and installing another version in
	parallel can cause all kinds of trouble.


`keg-only` はmacOSとの競合回避のためインストールしただけでは有効にならず、symlinkを貼る必要のあるパッケージのこと。  
`libxml2` はインストールされているが、有効になっていなかった  
symlinkを貼って、homebrewの `libxml2` が使用されるようにする

	$ brew link --force libxml2


## 使用方法
インストールが完了したら実際に脆弱性診断,ペネトレーションテストを行ってみる。  


基本（非侵入型のテスト）

	# --url or -u オプションで対象のurlを指定
	# [!] が表示されたものが発見された脆弱性
	$ ruby wpscan.rb --url http://www.example.com/


`wp-content` ディレクトリを指定する

	$ ruby wpscan.rb --url http://www.example.com/ --wp-content-dir path/to/wp-content


ユーザ一覧を調べる

	$ ruby wpscan.rb --url http://www.example.com/ --enumerate u


リストアップしたユーザに対して、辞書ファイルを使ってブルートフォースを行う。  

	# darkc0de.lst はパスワードを羅列したテキストファイル
	$ ruby wpscan.rb --url http://www.example.com/ --wordlist darkc0de.lst --threads 50


ユーザ名を指定してブルートフォースを行う

	$ ruby wpscan.rb --url http://www.example.com/ --wordlist darkc0de.lst --username admin


`crunch` コマンドで生成した辞書リストを使ってブルートフォース

	$ crunch 5 13 -f charset.lst mixalpha | ruby wpscan.rb --url http://www.example.com/ --wordlist -


使用しているプラグインを調べる（よく使われているもののみ）

	$ ruby wpscan.rb --url http://www.example.com/ --enumerate p


WPScanのデータベースを更新する

	$ ruby wpscan.rb --update


ログに残す

	$ ruby wpscan.rb --url http://www.example.com/ --debug-output 2>debug.log


## 参考	
- [WPScan by the WPScan Team](https://wpscan.org/)  
- [技術ブログvol.7 WPScanを使ってWordPressサイトをチェックしてみる](http://www.denet.ad.jp/technology/2013/11/vol7-wpscanwordpress.html)　　
- [rbenv+ruby-build で ruby のバージョンを2.4.0 に上げたときのメモ - Qiita](http://qiita.com/selmertsx/items/d4b281442a61da5f15b4)  
- [bundle install で nokogiri をインストールしろと言われる解決方法 - Qiita](http://qiita.com/pugiemonn/items/4b23a709ed194b02be6d)　　
- [積んメモ: keg-onlyなパッケージはシンボリックリンクが作られない](http://mosquito7.blogspot.jp/2013/07/keg-only.html)


