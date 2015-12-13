title: vim 学习 011 - vim 查找替换
date: 2015-12-13 15:27:30
categories: vim
tags: [vim-search, vim-replace]
---

本文介绍了 vim 查找替换操作。

<!--more-->

`f`/`F` 搜索字符
----
指令     | 说明
--------|------------------------
`t{char}` | Till **before** [count]'th occurrence of {char} to right.
`T{char}` | Till **before** [count]'th occurrence of {char} to left.
`f{char}` | To [count]'th occurrence of {char} to the right.
`F{char}` | To the [count]'th occurrence of {char} to the left.
`;`       | Repeat latest f, t, F or T [count] times.
`,`       | Repeat latest f, t, F or T in **opposite** direction [count] times.

`/`、`?` 搜索查找
----
命令             | 说明            | 备注
----------------|----------------|--------
`/phrase `      | searches FORWARD for the phrase | `\c`可以忽略大小写（ic，ignore case）
`?phrase `      | searches BACKWARD for the phrase | `\c`可以忽略大小写（ignore case）
`n`             | to find the next occurrence in the same direction：| `/`一直向下查找；`?`一直向上查找。
`N`             | to search in the opposite direction | `/`向上查找；`?`向下查找。
`*`             | search backward | 向下查找当前光标所在单词
`#`             | search backward | 向上查找当前光标所在单词

###[如何去掉当次搜索高亮？](http://forum.ubuntu.org.cn/viewtopic.php?p=815643)

> 方法一：`:noh` 或 `:nohl` 或 `:nohls` 或 `:nohlsearch`  
> 方法二：/awertgvcxz，查找一个根本不存在的字符串，消除高亮

`:s` 查找替换（THE SUBSTITUTE COMMAND）
----
按键             | 说明
----------------|------------------------
`:s/old/new`      | only changes the **first** occurrence of "old" in the line.
`:s/old/new/g`    | Adding the `g` flag means to substitute globally in the line, <br/>change all occurrences of "old" in the line.
`:#,#s/old/new/g` | where #,# are the **line numbers** of the range of lines where the substitution is to be done.
`:%s/old/new/g`   | to change every occurrence in the **whole file**.
`:%s/old/new/gc`  | to find every occurrence in the whole file, with a **prompt** whether to substitute or not.

