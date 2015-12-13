title: vim 学习 008 - vim 配置
date: 2015-12-13 14:37:15
categories: vim
tags: [vimrc]
---

本文介绍了 vim 配置的基本知识。

<!--more-->

在每个用户的主目录下，都有一个 vi 配置文件".vimrc"或".exrc"，没有的可以新建一个。
用户可以编辑它，使这些设置在每次启动 vi 时都有效。

vim配置可以在底行模式针对当前文档编辑设定，例如 `:set ai` 设定 `autoindent`，也可以设置到配置文件中（可省掉开头的冒号）。

配置语法
----
### options/features
可通过 `:help vimrc-intro` 查看 **vimrc** 配置帮助。

### set/enable
Typing ":set xxx" sets the option "xxx", or configure to `.vimrc`.

short  |  long（origin） |  comment
-------|----------------|-------------------------------------------
'ic'   | 'ignorecase'   |  ignore upper/lower case when searching
'is'   | 'incsearch'    |  show partial matches for a search phrase
'hls'  | 'hlsearch'     |  highlight all matching phrases

### unset/disable
Prepend "no" to switch an option off:

> :set noic

新建 .vimrc 或 .exrc
----

```Shell
➜  ~  cd ~
➜  ~  touch .vimrc
➜  ~  vi .vimrc
```

vim 配置格式可参考 `/usr/share/vim/vim73/vimrc_example.vim`。

设置示例
----
### 注释
在C++语言中，以 `//` 开头的行视为注释；在 vim 脚本中，以 `"` 开头的行视为注释。

### 设置显示行号（nu/nonu）
在打开 .vimrc（.exrc） 的 vim 编辑窗口中敲 `i` 键进入 **insert** 模式，打开布尔开关 `nu`:

```vim
"显示行号
set nu "number
```

按下 `<Esc>` 退回 **normal** 模式，再敲入 `:wq`（或 `ZZ` ）保存退出。
之后每次打开 vi/vim 时都会显示行号。

---
若要不显示行号，直接删除或注释 `set nu` 这一配置行即可恢复默认；或者使能 `nu` 对应的布尔开关 `nonu`：

```
"不显示行号
set nonu
```

### 设置tab宽度为4（ts/tabstop）
vi 中tab宽度默认为8，可以通过`set tabstop`赋值更改：

```vim
"设置tab宽度为4
set ts=4 "tabstop
```

你也可以根据需要，开启softtabs，即用4个空格展开tab（expandtab）：

```vim
"tab用空格展开
set et "expandtab
```

### 设置自动缩进（autoaudient）

```vim
 "自动对齐，把当前行的对齐格式应用到下一行
set ai "autoindent
```

[读取设置](http://blog.chinaunix.net/uid-22188554-id-461603.html)
----

1.查看环境变量  
	例如，要查看 $VIMRUNTIME 的值：
	
```vim
:echo $VIMRUNTIME
```

2.查看配置项的值  
	要查询某个配置开关或选项的值，只需要set该变量为问号(?)即可。  
	例如，查看 fileencodings 的值：
	
```vim
:set fileencodings ?
```

vim 帮助文档的编码格式为：

> fileencodings=ucs-bom,utf-8,default,latin1



## 参考
[实践中学习vim之vim配置文件、插件文件加载路径](http://blog.csdn.net/smstong/article/details/20567235)  
[优雅的配置 Vim](http://havee.me/linux/2013-10/configure-vim-gracefully.html)  
[vim set 功能设定](http://linux.chinaunix.net/techdoc/desktop/2006/11/21/944473.shtml)  
[某人的.vimrc：加详细注释](http://shansun123.iteye.com/blog/382650)  
[一套强大的vim配置文件+详细注释](http://xiaozhuge0825.blog.163.com/blog/static/5760606820101127105848596/)  
[很好很强大的vimrc（带注释版）](http://www.cnblogs.com/zourrou/archive/2011/04/16/2018493.html)  
[Vim 配置详解](http://www.cnblogs.com/witcxc/archive/2011/12/28/2304704.html)  
[vim配置的一些建议（附详细注释的vimrc）](http://liuyix.org/blog/2011/my-vimrc/)  
[Linux大棚版vimrc配置](http://roclinux.cn/?p=2675)  

[The Ultimate Vim Distribution](http://vim.spf13.com/)  
[vgod/vimrc](https://github.com/vgod/vimrc)  
[kaochenlong/eddie-vim](https://github.com/kaochenlong/eddie-vim)  
