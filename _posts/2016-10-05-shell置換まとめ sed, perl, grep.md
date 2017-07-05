---
layout: post
title: "shell置換まとめ sed, perl, grep"
date: 2016-10-05
categories: 
- Linux
published: true
---

## sed基本

ファイルを指定して、テキストを置換する

```sh
	$ sed -i -e "s/from/to/g" /path/to/name
```

複数条件を指定する

```sh
	$ sed -i -e "s/from1/to1/g" -e "s/from2/to2/g" /path/to/name
```

パイブで使う

```sh
	$ echo hogefuga | sed "s/hoge//g"
```



## grep して sed で置換する

### ファイルを上書きする場合

```sh
	# linux
	$ grep -rl '置換対象の文字列' ./ | xargs sed -i -e 's/置換対象の文字列/置換後の文字列/g'

	# mac
	$ grep -rl '置換対象の文字列' ./ | xargs sed -i '' -e 's/置換対象の文字列/置換後の文字列/g'
```

### .bakファイルを作成する場合

```sh
	# linux
	$ grep -rl '置換対象の文字列' ./ | xargs sed -i.bak -e 's/置換対象の文字列/置換後の文字列/g'
	
	# mac
	$ grep -rl '置換対象の文字列' ./ | xargs sed -i '.bak' -e 's/置換対象の文字列/置換後の文字列/g'
```

### ポイント
- macの場合は、`-i` オプションの後に、空文字を指定しないと動作しない。
- sedで置換されたファイルは、タイムスタンプが現在のものに置き換わる。



## findで検索したファイルをsedで置換する
findの print と  xargs -0 オプションで空白文字を含んだファイルパスを置換できる  

```sh
	# linux
	$ find . -type f -print0 | xargs -0 sed -i 's/置換前/置換後/g'

	# mac
	$ find . -type f -print0 | xargs -0 sed -i '' -e 's/置換前/置換後/g'
```


日本語ファイル名でうまく置換できないときはexecオプションを使用する

```sh
	$ find . -type f -exec sed -i '' -e "s/置換前/置換後/g" {} \;
```
  
`sed: RE error: illegal byte sequence` エラーが出る場合

```sh
export LANG=C
```


## 特殊な文字を置換する

e.g. `'share' . DS . 'cakephp' . DS . 'current'`

- シングルクォートやダブルクォート

```sh
	# シングルクオートが含まれるので、ダブルクオートで対象文字列を囲む
	# `.` はエスケープする
	$ sed -i "s/'share' \. DS \. 'cakephp2'/'share' \. DS \. 'cakephp' \. DS \. 'current'/g" ./sandbox.com/contact/index.php

	# grepと組み合わせる
	$ grep -rl "'share' . DS . 'cakephp2'" . | xargs sed -i "s/'share' \. DS \. 'cakephp2'/'share' \. DS \. 'cakephp' \. DS \. 'current'/g"
	$ grep -rl "'current' \. 'lib'" . | xargs sed -i "s/'current' \. 'lib'/'current' \. DS \. 'lib'/g"
```



### スラッシュを含む文字の場合
スラッシュとシングルクオートを含む文字列を置換対象にする場合、
1. 文字列を　ダブルクオートで囲む
2. 区切り文字を / から : に変更する
とうまく行った。

```sh
	$ grep -rl "<?php get_template_part('footer'); ?>" ./ | xargs sed -i '' -e "s:<?php get_template_part('footer'); ?>:<?php require_once TEMPLATEPATH . '/inc/pages/footer.php'; ?>:g"
```


  
### 特定のテキストを改行コードに置換する

sedの場合、改行コードへの置換が、`\n` で行えない。  
改行コードをエスケープして実行すると改行に置換ができる  

```sh
	$ export -p | grep PATH | sed 's/:/\
	/g'
```

ただ、これだとターミナルで 改行を入力できないのでその場合 `tr` で行う

```sh
	# trはファイルを引数に取れないのでパイプで実行する
	$ export -p | grep PATH | tr ':' '\n'
```



## 正規表現と後方参照で、user idだけを出力する

```sh
	# \(.*\) 後方参照する
	# \1 後方参照を取り出す
	$ id username | awk '{print $1}' | sed -e 's/^uid=\(.*\)(.*)$/\1/g'
```

```sh
	# perlのconfigからサーバパスワードを取得する例
	$ cat /home/username/private/config.conf | grep "SERVER_PASS" | sed -e "s/\tSERVER_PASS => '\(.*\)',/\1/"
```


## perlで複数行置換

```sh
	perl -0p -i -e "s/from/to/gm" /path/to/file
```


## 参考
[複数ファイルの文字列を一括置換 - Qiita](http://qiita.com/HayneRyo/items/f13f36b969d0ab029553)
[grepとsedでファイルの内容を一括変換:Mac OS X | ゆるメモ](http://notes.applifirst.jp/2014/06/09/43/)  
[sed の s コマンドの区切り文字は変更できる | CUBE SUGAR STORAGE](http://momijiame.tumblr.com/post/68363821454/sed-%E3%81%AE-s-%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%81%AE%E5%8C%BA%E5%88%87%E3%82%8A%E6%96%87%E5%AD%97%E3%81%AF%E5%A4%89%E6%9B%B4%E3%81%A7%E3%81%8D%E3%82%8B)　　
[sedコマンドの備忘録 - Qiita](http://qiita.com/takech9203/items/b96eff5773ce9d9cc9b3)

### macのsed
[find ~ xargs ~ sedをやろうとしたら厳しかったのでRubyを使った話 - Qiita](http://qiita.com/giiko_/items/6b6ff23c707932491b6f)  

### 改行について  
[sedコマンドで改行(LF)に変換したいと思ったら、trコマンド - RLB](http://rksz.hateblo.jp/entry/2013/03/11/085556)  

### 後方参照
[sed | テキストの置換,正規表現のメタ文字や後方参照の方法など](http://bi.biopapyrus.net/linux/sed.html)  

### perl
[perlのワンライナーで複数行の置換を行う - Qiita](http://qiita.com/takc923/items/e608568a9734e2d84f7f)  
[Perl ワンライナーで複数行置換 - Qiita](http://qiita.com/maru3/items/d19f640d6cc7d4ea90a4)  
[パターンの中のエスケープ処理 - 正規表現の基本 - Perlにおける正規表現](http://www.perlplus.jp/regular/ini/index5.html)  
[【保存版】正規表現でエスケープが必要な文字一覧表 - Qiita](http://qiita.com/katsukii/items/1c1550f064b4686c04d4)  

