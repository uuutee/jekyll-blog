---
layout: post
title:  "sublime text で拡張子ごとにsyntaxを設定する"
date:   2016-02-13
categories: "sublime text"
published: true
---

Application Support内に、適用したい `syntaxの名前.sublime-settings` を作成

	~/Library/Application Support/Sublime Text 3/Packages/User/JavaScript.sublime-settings

内容は下記の通り、 extensions に 適用したい拡張子を配列で指定。

```json
{
	"extensions":
	[
		"jsf"
	]
}

```

