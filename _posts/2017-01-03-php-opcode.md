---
layout: post
title:  "PHP编程原理之opcode"
date:   2017-01-03 10:11:40 +0800
categories: [normal]
excerpt: "PHP编程原理之opcode，今天只是了解了一点点。"
tags:
  - PHP
---

昨天翻开很早之前买的一本书 《编写高质量的PHP接口》，呵呵，书名忘记，反正有‘PHP’！

直接翻开就是我之间放的一个书签，opcache这一个章节，我之前没看懂，但是昨天一看反而基本清晰了，至少知道在讲什么了，不知道是不是之前没认真看。

今天来公司之后，虽然精神状态有点不好（打瞌睡），还是搜了opcode的一些相关信息看了看。

先说说，我看了之后自己的理解吧。

计算机可执行的代码，叫做机器代码，所有语言的执行最终都会转换成这种机器代码。PHP也有类似的，会编译成一个PHP的zend引擎可执行的代码，就是这个了，相当于中间代码。自然的书上所的opcache就是缓存的这个opcode，可以直接执行的，所以就提高了执行效率。

鸟哥文章中写的。
[http://www.laruence.com/2008/06/18/221.html](http://www.laruence.com/2008/06/18/221.html)
```
1.Scanning(Lexing) ,将PHP代码转换为语言片段(Tokens)
2.Parsing, 将Tokens转换成简单而有意义的表达式
3.Compilation, 将表达式编译成Opocdes
4.Execution, 顺次执行Opcodes，每次一条，从而实现PHP脚本的功能。
```

可以看出，如果缓存了opcode，就直接跳过了前面三步。