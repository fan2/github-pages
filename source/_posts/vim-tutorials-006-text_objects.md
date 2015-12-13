title: vim 学习 006 - vim 文本对象（Text Object）
date: 2015-12-13 14:21:40
categories: vim
tags: vim-text-objects
---

本文介绍了 vim 的文本对象及基于文本对象的块操作。

<!--more-->

参考：`/usr/share/vim/vim73/doc/motion.txt`

**文本对象**的存在使得你可以**选择**或**操作**更加复杂的对象。比起选中某个单词并删除、跳转到句首并删除句子等简单操作来说，不管你在何种文本对象的区域内，你都可以对它们执行这些操作。

> This is a series of commands that can only be used while in **Visual mode** or after an **operator**.
> The commands that start with "a" select **"a"n** object **including** white space, the commands starting with "i" select an **"inner"** object **without** white space, or just the white space.
> Thus the "inner" commands always select less text than the "a" commands.
> These commands are not available when the |+***textobjects***| feature has been disabled at compile time.

**`a`和`i`的区别**：

==a==n object：包含尾部间隔空格
==i==nner object：只是内容本身，不包含尾部单词间隔空格

## word / sentence / paragraph

textobject  | 说明
------------|-------------
`w`           | **w**ord
`s`           | **s**entence
`p`           | **p**aragraph

## block / Block

textobject  | 说明
------------|--------------
`]`/`[`         | [] block
`)`/`(`、`b`      | block
`>`/`<`、`>`/`<`    | <> block
`}`/`{`、`B`      | Block
`t`           | tag block：<> </>

## quoted string

textobject  | 说明          
------------|--------------
`"` / `'` / `` ` `` | quoted string

## visual mode
在可视选择模式下，可以以 `v` 做前缀，`a` 或 `i` 限定边界，后续指定操作对象，来实现针对文本对象的选择： 

指令  | 说明
-----|----------------------------
`viw`  | 选中单词
`vis`  | 选中句子
`vip`  | 选中段落
`vi(`  | 选中圆括号中的内容
`vi[`  | 选中中括号之间的内容
`v2i{` | 选中两层大括号之间的所有内容<br/>数字限定选择的嵌套层数
`v3aw` | 选择三个单词（包含中间的2个间隔空格）
`v3iw` | 选择三个单词（2个单词+间隔空格）

另外，可将 `v` 选择操作指令替换为 `x`、`d`、`y` 等操作符(operator)，来针对文本对象操作。
