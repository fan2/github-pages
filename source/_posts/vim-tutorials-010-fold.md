title: vim 学习 010 - vim 折叠
date: 2015-12-13 14:58:50
categories: vim
tags: vim-fold
---

本文介绍了 vim 块折叠的基本操作。

<!--more-->

[vim fold](http://blog.csdn.net/fangjuniceflame/article/details/4560891)
---

最近需要折叠，于是[找了一下](http://www.linux.com/article.pl?sid=06/05/18/1915233)。其实手册里面有，不过手册做的太好太详细的后果往往就是让人懒得看了。写的东西超过五六屏以后，感觉翻来翻去就不怎么方便了，看起来也很混乱。这个时候最好就能折叠起来。VIM里面也有VS里面那样的折叠功能，而且不需要用鼠标去点那个减号和加号。

## foldenable
This option can be used to quickly switch between showing all text unfolded and viewing the text with folds (including manually opened or closed folds).  
It can be toggled with the |*`zi`*| command. 

折叠功能可以通过底行模式命令 `:set foldenable`/`:set nofoldenable` 来开启/禁用，也可通过 `zi` 指令开启。

当然，你也可以将 `foldenable` 配置到 `.vimrc` 文件中：

```vim
"启用折叠
set fen "foldenable
```

后期，针对具体的 vim 编辑窗口，你也可以在底行模式下通过命令 `:set nofoldenable` 临时禁用。

## [foldmethod](http://blog.sina.com.cn/s/blog_70808ace0100qsdg.html)
可以让 VIM 根据语法缩进，标记自动折叠。用 `set foldmethod=foldoption` 来设定。  
可以 `:h fold-methods` 查看一下 foldmethod 说明。

```
The folding method can be set with the 'foldmethod' option.

There are six methods to select folds:
        manual          manually define folds
        indent          more indent means a higher fold level
        expr            specify an expression to define folds
        syntax          folds defined by syntax highlighting
        diff            folds for unchanged text
        marker          folds defined by markers in the text

MANUAL                                          *fold-manual*

Use commands to manually define the fold regions.  This can also be used by a script that parses text to find folds.

INDENT                                          *fold-indent*

The folds are automatically defined by the indent of the lines.

The foldlevel is computed from the indent of the line, divided by the
'shiftwidth' (rounded down).  A sequence of lines with the same or higher fold level form a fold, with the lines with a higher level forming a nested fold.

The nesting of folds is limited with 'foldnestmax'.
```

可用选项 'foldmethod' 来设定折叠方式：`:set fdm=xxx`，fdm 是 foldmethod 的缩写。  
也可直接在源代码文件中使用注释调用 vim 命令： 

```C
/* vim: set fdm=XXX: */
```

**有 6 种方式选定折叠：**

折叠方式           | 说明
------------------|----------------------
手工折叠 / manual | Folds are created manually.
缩进折叠 / indent | Lines with equal indent form a fold.
范式折叠 / expr   | 'foldexpr' gives the fold level of a line.
标记折叠 / marker | Markers are used to specify folds.
语法折叠 / syntax | Syntax highlighting items specify folds.
差异折叠 / diff   | Fold text that is not changed.

可针对当前 vim 编辑文档，在底行模式使用 `:set fdm=marker` 命令来设置成 `marker` 折叠方式。  
当使用 marker 方式时，需要用标计来标识代码的折叠，系统默认是 {{{ 和 }}}，即基于 Block 文本对象折叠，最好不要改动。  
可以每次针对当前 vim 编辑窗口，在底行模式通过 `:set fdm=XXX` 来设置折叠方式，也可将其配置到 .vimrc 文件中。后面针对特定文件也可在底行模式动态变更折叠方式。

**注意：**

> 每一种折叠方式不兼容，如不能既用 `expr` 又用 `marker` 方式，可以轮流使用 `indent` 和`marker` 方式进行折叠。

### indent tabstop
[vim的shiftwidth、tabstop、softtabstop设置](http://blog.csdn.net/ludonghai715/article/details/5657712)  
[vim中tabstop、shiftwidth、softtabstop以及expandtab的关系](http://blog.csdn.net/chenxiang6891/article/details/41348073)

建议 `.vimrc` 中配置使用 hardtabs：

```vim
"不使用空格代替制表符，使用原生hardtabs；
"若开启et，插入真正的制表符，需要使用CTRL-V<Tab>。
set noet "noexpandtab

"制表符以空格度量的位宽
set ts=4 "tabstop

"自动缩进所使用的空白长度指示
set sw=4 "shiftwidth

"一旦设置了softtabstop的值，按下tab键将智能插入空格和制表符的混合
set sts=4 "softtabstop
```

### [manual space](http://www.cnblogs.com/witcxc/archive/2011/12/28/2304704.html)

```vim
"启用折叠
set foldenable
"手动折叠
set foldmethod=manual
"用空格键来开关折叠
nnoremap <space> @=((foldclosed(line('.')) < 0) ? 'zc':'zo')<CR> 
```

## fold-commands

### ENABLE/DISABLE FOLDS

command | function
--------|---------------------------------------------
`zn`      | Fold none: reset 'foldenable'.<br/>All folds will be open.
`zN`      | Fold normal: set 'foldenable'.<br/>All folds will be as they were before.
`zi`      | Invert 'foldenable'.

### OPENING AND CLOSING FOLDS
所有的折叠相关的指令都是以 `z` 开头，`z` 如其形，看着就像折纸。  
`f` 的意思是 fold，所以 `zf` 就是折叠。你可以在 visual 模式下选定，再按 `zf` 指令，可以将选定内容进行折叠。  

`zf`{motion}  or {Visual}`zf`：

- Operator to create a fold.
- This only works when 'foldmethod' is "manual" or "marker".
- The new fold will be closed for the "manual" method.
- 'foldenable' will be set.

command | function
--------|---------------------------------------------
`zo`      | Open *one* fold under the cursor.
`zO`      | Open *all* folds under the cursor **recursively**.
`zc`      | Close *one* fold under the cursor.
`zC`      | Close *all* folds under the cursor **recursively**.
`za`      | When on a closed fold: open it;<br/>when on an open fold: close it.<br/>When folds are *nested*, you may have to use "za" several times.
`zA`      | When on a closed fold: open it **recursively**.<br/>When on an open fold: close it **recursively** and set 'foldenable'.

`zm`      | Fold more: Subtract one from 'foldlevel'.<br/>If 'foldlevel' was already zero nothing happens.<br/>'foldenable' will be set.
`zM`      | Close all folds: set 'foldlevel' to 0.<br/>'foldenable' will be set.
`zr`      | Reduce folding: Add one to 'foldlevel'.
`zR`      | Open all folds. This sets 'foldlevel' to highest fold level.

### MOVING OVER FOLDS 

command | function
--------|---------------------------------------------
`[z`      | Move to the start of the **current** open fold.
`]z`      | Move to the end of the **current** open fold.
`zj`      | Move downwards to the start of the **next** fold.<br/>A closed fold is counted as one fold.
`zk`      | Move upwards to the end of the **previous** fold.<br>A closed fold is counted as one fold.

折叠可以嵌套，`zr` 可以打开第一层的折叠，`zR` 可以打开所有层的折叠。与之对应的是 `zm` 和`zM`，是按层关上折叠。  
比如说按 `zm`，会从最里层的层叠关起；连续zm就会一直关到最上层的折叠。  
查找到了折叠里面会将折叠打开，处理完了之后要自己将折叠关上。


### Folding Patterns

- 可以用普通的定位方法来告诉VIM你要折叠哪些地方，比如 `zj` 将跳到下一个可折叠点，`zk` 将跳到上一个可折叠点。  
- 可以复合数字 [count] 指定 fold depth 或 repeat times，例如 `zf9j` 将下面9行的内容折起来。  
- 可以用 marker，还可以用行号:20,50，就可以把20行到50行折叠起来。  
- 也可以针对 text object 进行折叠，例如 `zfa}` 指令可以将下面匹配的花括号之间的 Block 折叠起来，这样可以很方便的折叠函数。  
- 其他的折叠方法还有 `zf/string`，可以折叠到下一个匹配的string，不过好像不太实用。

## restore/load folding
要在下一次打开的时候还保留折叠，可以在离开的时候 `:mkview` ，再进来的时候 `:loadview` 就可以了。  
当然也可以将这两句配置到 `vimrc` 里面让其自动完成。  
这样每次就可以看到上次的折叠了。

command              | function
---------------------|------------
`:mkvie[w][!] [file]`  |    Write a Vim script that restores the contents of the current window.
`:lo[adview] [nr]`     | Load the view for the current file.<br/>When [nr] is omitted, the view stored with ":mkview" is loaded.<br/>When [nr] is specified, the view stored with ":mkview[nr]" is loaded.

## 参考
[VIM fold 折叠](http://blog.csdn.net/jerry_zjunwei/article/details/7626111)  
[VIM设置代码折叠](http://www.cnblogs.com/fakis/archive/2011/04/14/2016213.html)  
[VIM学习笔记之折叠](http://blog.sina.com.cn/s/blog_7acf472901017ad6.html)  
[原来Vim的fold可以这么拉风！](http://www.douban.com/group/topic/16849790/)  
