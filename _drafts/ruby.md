# 2017-03-06 環境変数を確認
rubyの環境変数を確認

	$ gem environment



# 2017-03-06 bundlerとGemfile

Gemfile: インストールするgemが書かれたファイル。composer.json みたいなもの
Gemfile.lock: インストールすると生成されるファイル。 composer.json.lock と同じ。
Bundler: Gemfile に書かれたgemをインストールする

## 参考
[Bundlerの使い方 - Qiita](http://qiita.com/oshou/items/6283c2315dc7dd244aef)



# 2017-03-06 gem関連

一覧を確認

	$ gem list


特定のgemのインストール先を確認

	$ gem which name


gemをインストール

	$ gem install name


gemを削除

	$ gem uninstall kramdown





# 2017-03-06 rbenv
利用可能な ruby のバージョンを確認。現在使用中のバージョンも確認できる

	$ rbenv versions


## 参考
[Rubyが参照してるgemのパスやインストール済gemを確認する方法 - Qiita](http://qiita.com/h5y1m141@github/items/74029cab9706971c8dbe)
