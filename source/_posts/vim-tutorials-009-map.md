title: vim 学习 009 - vim 映射
date: 2015-12-13 14:44:52
categories: vim
tags: vim-map
---

本文介绍了 vim 按键映射的基本知识。

<!--more-->

[vim按键映射](http://haoxiang.org/2011/09/vim-modes-and-mappin/)
===
map前缀
----
对vim map而言，前缀作为命令本身的修饰符，微调命令的作用模式和效果。
vim map针对模式和递归效果可能有这么几种前缀：

前缀  | 意义
-----|---------------------
nore | 表示非递归，**默认递归**（Recursive Mapping）
n    | 表示在普通模式下生效
i    | 表示在插入模式下生效
c    | 表示在命令行模式下生效
v    | 表示在可视模式下生效

### Recursive Mapping
递归的映射，其实很好理解：

```vim
"键`a`被映射成了`b`
:map a b
```

```vim
"`c`又被映射成了`a`
:map c a
```

上述map默认没加`nore`前缀，即递归映射，那么`c`最终就被映射成了`b`。对于按键`c`等效于：

```vim
:map c b
```

**说明：**
> 默认map命令影响到普通模式和可视模式。
> 在自定义映射时，递归迂回一般都是非预期的，故通常使用非递归（`noremamp`）。

如果不是特殊情况，在使用vim时，手指应尽量集中在键盘中间排。可以将较少使用的`CapsLock`键映射为较难触摸的左上角`<Esc>`键：

### unmap
unmap后面跟一个按键（组合），表示删除这个按键（组合）的映射。有点类似C语言预编译宏中的取消宏定义符号
`#undef`。
在map生效模式下，执行以下命令，解除`c`到`a`的映射：

```vim
"解除c键映射
:unmap c
```

同样，unmap可以加各种前缀，表示影响到的模式。

### mapclear
mapclear直接清除相关模式下的所有映射。
同样，mapclear可以加各种前缀，表示影响到的模式。

常用map命令
----

prefix | map   | noremap   | unmap   | mapclear
-------|-------|-----------|---------|-----------
-      | :map  | :noremap  | :unmap  | :mapclear
n      | :nmap | :nnoremap | :nunmap | :nmapclear
v      | :vmap | :vnoremap | :vunmap | :vmapclear
i      | :imap | :inoremap | :iunmap | :imapclear
c      | :cmap | :cnoremap | :cunmap | :cmapclear

vim的map模式的相关命令：
[![vim的map模式的相关命令](http://roclinux.cn/wp-content/uploads/2013/01/Snip20130103_2.png)](http://roclinux.cn/wp-content/uploads/2013/01/Snip20130103_2.png)