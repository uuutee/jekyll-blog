---
layout: post
title: "/tmp/ と /var/tmp/"
date: 2016-05-18
categories: 
- Linux
- Mac
published: true
---

- unix系OSには、一時ファイル置き場として /tmp/ と /var/tmp/ があるが、基本的にはどちらでもいい
- /tmp/ は再起動時に消える
- /var/tmp/ は再起動時には消えない。
- /var/tmp/ の方が削除期間は長めに設定されている。

## 削除
実際に /tmp/ を削除しているのは、 `/usr/sbin/tmpwatch` というコマンド。  
スケジュール自体は、`/etc/cron.daily` と `/etc/rc.d/rc.sysint` に登録されている。  
削除期間はここから設定できる。

## Mac OSXでは
Mac OSXにも、 `/private/tmp/` と `/private/var/tmp/` が存在する。  

`/tmp/ => /private/tmp/` は、 `/etc/defaults/periodic.conf` のスケジュールに削除期間(3日)等、設定されている。  

```
# 110.clean-tmps
daily_clean_tmps_enable="YES"                           # Delete stuff daily
daily_clean_tmps_dirs="/tmp"                            # Delete under here
daily_clean_tmps_days="3"                               # If not accessed for
```

`/private/var/tmp/` は定期的に削除されない（おそらくアプリケーション用のテンポラリ）ので、osxの一時ファイルは、 `/tmp/` に置くようにする。  


## sickyビット
パーミッションが `drwxrwxrwt` となっているて、これの `t` は、スティッキービットという。
これはすべてのユーザーが自由に操作できるようにするものの、ディレクトリの削除はrootなど、  作成したユーザーのみ行えるようにしたい。その場合につける特殊なフラグ。  
8進数で表現すると `1000` になるので、 `chmod 1000 file` とすると付与できる。  

## 参考
[/tmpと/var/tmpの仁義無き戦い - Qiita](http://qiita.com/kuni-nakaji/items/f29be14be578b5a19d4b)  
[Mac では /tmp は３日後に削除される | deadwood](http://www.d-wood.com/blog/2014/04/09_6008.html)  
[Mac OSXの空き容量が足りない。容量を喰ってるフォルダを探す - それマグで！](http://takuya-1st.hatenablog.jp/entry/2015/07/14/153554)  
[スティッキービット - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%B9%E3%83%86%E3%82%A3%E3%83%83%E3%82%AD%E3%83%BC%E3%83%93%E3%83%83%E3%83%88)



