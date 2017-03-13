# 2016-12-11 nokogiri がインストールできない

目次生成プラグイン用に `nokogiri` をインストールする

	$ gem install nokogiri

権限エラーが出る

	ERROR:  While executing gem ... (Errno::EACCES)
	Permission denied @ dir_s_mkdir - /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static/nokogiri-1.6.8.1

パーミッションを確認
			
	Path:        /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15
	File:        x86_64-darwin-15
	Permission:  drwxr-xr-x
	Owner:       root:staff
				
	Path:        /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static
	File:        2.2.0-static
	Permission:  drwxr-xr-x
	Owner:       root:staff

インストール先の所有者がrootかつ755になっているのが原因  
  
グループの書き込み権限を付与する  

	$ sudo chmod g+w /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/
	$ sudo chmod g+w /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static

再度実行するとインストールできた

	$ gem install nokogiri


# 2016-12-11 jekyllでカテゴリアーカイブを作る

## 参考
[[Jekyll] jekyllでタグ・カテゴリ・マンスリーアーカイブページを作る | UI/UX Design、フロントエンド系の技術に関する備忘録 | whiskers](https://whiskers.nukos.kitchen/2015/01/31/jekyll-archives.html)



# 2016-12-06 jekyllを使う

インストール

	$ mkdir jekyll
	$ gem install jekyll

エラーが出てインストールできない

	ERROR:  Error installing jekyll:
	ERROR: Failed to build gem native extension.

    /Users/ut/.rbenv/versions/2.2.0/bin/ruby -r ./siteconf20161204-20241-boub7r.rb extconf.rb
	checking for ffi.h... *** extconf.rb failed ***

	Could not create Makefile due to some reason, probably lack of necessary
	libraries and/or headers.  Check the mkmf.log file for more details.  You may
	need configuration options.

	/Users/ut/.rbenv/versions/2.2.0/lib/ruby/2.2.0/mkmf.rb:456:in `try_do': The compiler failed to generate an executable file. (RuntimeError)
	You have to install development tools first.
		from /Users/ut/.rbenv/versions/2.2.0/lib/ruby/2.2.0/mkmf.rb:587:in `try_cpp'

		~

		from extconf.rb:16:in `<main>'

	extconf failed, exit code 1

gemとgemのキャッシュを削除

	$ rm -rf /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/gems/ffi-1.9.1
	$ rm -rf /Users/ut/.rbenv/versions/2.2.0/lib/ruby/gems/2.2.0/cache/ffi-1.9.14.gem

ログを確認する

	$ less /Users/ut/.rbenv//versions/2.2.0/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static/ffi-1.9.14/mkmf.log

	package configuration for libffi is not found

homebrewでffiを検索

	$ brew search ffi

Xcodeライセンスのエラーが出る

	Error: You have not agreed to the Xcode license. Please resolve this by running:
  	sudo xcodebuild -license

Xcodeを起動してAgree

再度インストールでうまく言った

	$ gem install jekyll

	Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in your configuration file.


## プレビューする

	$ jekyll serve



## jekyll-paginate をインストール

	$ gem install jekyll-paginate



## 参考
[Jekyll • シンプルで、ブログのような、静的サイト](http://jekyllrb-ja.github.io/)  
[30分のチュートリアルでJekyllを理解する](http://melborne.github.io/2012/05/13/first-step-of-jekyll/)  



# 2017-03-06 テンプレートを使って、サイトを作る
ヘルプを見ると new オプションでさくっと作れそう

	$ jekyll -h


作ってみる

	$ jekyll new /Users/ut/.ghq/github.com/uuutee/lightbox-sample/jekyll


エラーが出るので必要なgemをインストールしする

	$ gem install bundler
	$ gem install minima
	$ gem install jekyll-feed



# 2017-03-06 サブディレクトリ向けにurl変更する
ローカルサーバを立ち上げた場合と、github pages 等のurlが階層が異なる


local: http://127.0.0.1:4000/
githubpages: http://uuutee.github.io/lightbox-sample/


```yaml
baseurl: /example
```


[Clearing Up Confusion Around baseurl – Again - By Parker](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)  