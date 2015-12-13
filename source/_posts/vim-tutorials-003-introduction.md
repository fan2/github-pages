title: vim 学习 003 - vim 初识
date: 2015-12-13 13:53:22
categories: vim
tags: [vim, vim-version, vim-help, vim-read, vim-edit]
---

本文介绍查看 vim 版本、目录信息，学习参考教程（Usage Help/Tutor）的使用，并初步给出了使用 vim 进行编辑和阅读的引子。

<!--more-->

## Where is vim（on Mac OS X）

```Shell
➜  ~  whereis vim
/usr/bin/vim
➜  ~  ls /usr/bin/vim*
/usr/bin/vim      /usr/bin/vimdiff  /usr/bin/vimtutor
```

终端输入 **`vimtutor`** 命令可查看vim入门教程：

	➜  ~  vimtutor

![vimtutor](http://7xo5uz.com1.z0.glb.clouddn.com/vimtutor.png)

输入 `:q` 退出。

**说明：**

> 通過"vimtutor"來啟動教程，每次都会生成一份副本，方便边学习边实践。本人的 tutor 副本：
>> /private/var/folders/k6/7f8bh1ws4ygfg9pcq48w5tk00000gn/T/tutorr***

> 退出 tutor 时，该副本`tutorr***`将会被删除。

## vim version

	➜  ~  vim --version

![vim --version](http://7xo5uz.com1.z0.glb.clouddn.com/vim_--version.png)

### 版本信息

> Normal version without GUI.  Features included (+) or not (-):

Mac OS X 默认安装的版本是 `normal version`，以上列出了包含（+）和未包含（-）的features。

vim 一共有五个版本

缩写 | 全称
----|----
T   |  tiny
S   |  small
N   |  **normal**
B   |  big
H   |  huge

因为 vim 中有些插件会使用 python 和 ruby，我们可以[编译升级 Mac 上的 vim 到7.4 huge版](http://blog.csdn.net/u011542994/article/details/39058779)，启用 python 和 ruby 的支持特性。

### 配置文件
倒数第六行开始的四行给出了配置文件的路径：

```Shell
    system vimrc file: "$VIM/vimrc"
      user vimrc file: "$HOME/.vimrc"
       user exrc file: "$HOME/.exrc"
   fall-back for $VIM: "/usr/share/vim"
```

- 系统级别的 vimrc 配置文件：

> $VIM/vimrc

Mac默认没有指定`$VIM`。

- 用户级别的 vimrc 配置文件：

> $HOME/.vimrc

`$HOME` 即 `~`。

- fall-back：编译时指定的安装路径，即 `$VIM`=_/usr/share/vim_。

```Shell
➜  vi  cd /usr/share
➜  share  ls vim*
vim73 vimrc
```

其中 `$VIMRUNTIME` = `$VIM/vim73`。
关于 *`vimrc`* 和 *`exrc`*、`$VIM` and `$VIMRUNTIME`，可参考 `/usr/share/vim/vim73/doc/starting.txt`。

### vim73目录：

目录		 | 作用        | 说明
----------|-------------|-------
colors    | 配色方案	 | :colorscheme
doc       | 帮助文档     | :help
tutor     | 入门教程     | vimtutor
plugin    | 插件目录     | Vim Script脚本格式

其中 `*.vim` 为 [Vim Script](http://vim-scripts.org/) 脚本格式文件，例如：

> 1. ***filetype***: 文件类型检查
>> **filetype.vim**: detect file types
>> **ftoff.vim**:    switch off detection of file types

> 2. ***ftplugin***: 文件类型检查插件
>> **ftplugin.vim**: switch on loading plugins for file types
>> **ftplugof.vim**: switch off loading plugins for file types

> 3. ***indent***: 不同语言语法缩进
>> **indent.vim**:   switch on loading indent files for file types
>> **indoff.vim**:   switch off loading indent files for file types

> 4. ***syntax***：不同语言语法高亮检测插件
>> **syntax**:       c.vim / cpp.vim / objc.vim / objcpp.vim / markdown.vim

## vim help
### helphelp
在 Shell Prompt 中输入 `vim -R /usr/share/vim/vim73/doc/helphelp.txt`（`-R`选项表示 readonly），可以查看vim的帮助系统介绍。

### 如何查看帮助
按下 `<F1>` 键或在 vim 底行模式输入 `:help`（`:h`），可以查看帮助文档。  
vim 将新起分屏（window）以只读方式打开 `/usr/share/vim/vim73/doc/help.txt` 文件。

> Open a window and display the help file in read-only mode.

按下组合键 `<C-w>w`（按下 `ctrl+w`，再按一次 `w`），可在当前 vim 窗口和 help 分屏窗口之间切换；输入 `：q` 或 `:close` 可以关闭帮助窗口。

### help 示例
help 后也可带参数 `:help <argument>` 查看任何相关帮助（split screen 打开对应帮助文档）：

- 查看如何使用帮助：  
	> :help `help`

- 查看 vimdoc 的标记体例：  
	> :h `key-notation`

- 查看 `h` 按键的作用（motion left）  
	> :h `h`

- 查看 `noh` 命令的作用（`:noh`/`:nohlsearch`）  
	> :h `noh`

- 查看常规模式下 `w` 按键的作用（words forward）  
	> :h `w`

- 查看底行模式下 `w` 命令的作用（`:w`/`:write`）  
	> :h `:w`

- 查看 `vimrc` 相关的 topics  
	> :h `vimrc`

- 查看 `CTRL-C` 组合按键（Ctrl+c，一般写作 `<C-c>`，体例可 `:h key-notation`）命令在 normal、insert、visual、command-line 模式下的作用：  
	> :help `CTRL-C`
	> :help `i_CTRL-C`
	> :help `v_CTRL-C`
	> :help `c_CTRL-C`

其他帮助文档归类：

> Getting Started ~: `usr_1*.txt`
> Editing Effectively ~: `usr_2*.txt`
> Tuning Vim ~: `usr_4*.txt`
> Making Vim Run ~(Installing Vim): `usr_90.txt`

### vimtutor
`/usr/share/vim/vim73/tutor` 目录下存放着多语言版本的帮助教程，中文 Mac OS X 系统终端输入 **`vimtutor`** 命令，将打开对应繁体中文版本《tutor.zh.utf-8》（`vimtutor zh`）的副本：

```
➜  ~  cd /usr/share/vim/vim73/tutor
➜  tutor  vim tutor.zh.utf-8

===============================================================================
=      歡     迎     閱     讀   《 V I M  教  程 》   ──     版本 1.5      =
===============================================================================
```

我们也可以通过 `vimtutor en` 指定打开en英文版《tutor.utf-8》教程：

```
===============================================================================
=    W e l c o m e   t o   t h e   V I M   T u t o r    -    Version 1.7      =
===============================================================================
```

### vim在线帮助
[Vim documentations](http://www.vim.org/docs.php)  
[Vim documentation: help](http://vimdoc.sourceforge.net/)

## hi, vim
### 打开vim编辑器
通常情況下，您可以打开终端，输入︰

	vim tutor <回車>

這裡的 '**vim**' 表示進入vim編輯器（start the Vim editor），而 **tutor** 則是您準備要編輯的文件（the file you wish to edit）。

以下是 `vim --help` 摘录：

```Shell
➜  ~  vim --help
usage: vim [arguments] [file ..]       edit specified file(s)

Arguments:
   --			Only file names after this
   -v			Vi mode (like "vi")
   -e			Ex mode (like "ex")
   -s			Silent (batch) mode (only for "ex")
   -d			Diff mode (like "vimdiff")

   -R			Readonly mode (like "view")

   -m			Modifications (writing files) not allowed
   -M			Modifications in text not allowed
   -b			Binary mode
   
   -n			No swap file, use memory only
   -r			List swap files and exit
   
   -p[N]		Open N tab pages (default: one for each file)
   -o[N]		Open N windows (default: one for each file)
   -O[N]		Like -o but split vertically
   
   +			Start at end of file
   +<lnum>		Start at line <lnum>
```

### 使用 vim 编辑
1. 打开终端，输入 `vim hivim.txt`（假设当前家目录下尚不存在 hivim.txt 文件），vim默认为普通模式（normal mode）。vim将为 New File（`hivim.txt`）新建一个 **buffer**，此时尚不存在文件 `~/hivim.txt`。光标停留在第一行，下面的波浪线 `~` 表示空行。
2. 按键 `i` 进入编辑模式（insert mode），输入 `hello world from vim!`。
3. 按下 `esc` 键盘切回普通模式，输入 `:wq` 保存退出（write and quit）。
4. 输入 `cat hivim.txt` [^cat] 查看刚才使用 vim 新建-编辑-保存 的文件。

```Shell
➜  ~  cat hivim.txt
cat: hivim.txt: No such file or directory
➜  ~  vim hivim.txt
➜  ~  cat hivim.txt
hello world from vim!
➜  ~  
```

**`:w` 说明：**
> - 使用 vim 打开 \<vimfile\> 进行编辑时，vim 会自动在当前文件路径下创建一个名为`.<vimfile>.swp` 的缓存副本（swap buffer），最终需要调用 `:w`（`:write`）命令将副本中的编辑（diff）写入（patch）文件保存。  
> - 调用 `vim` 命令打开文件时，可以指定 `-n` 选项，此时无需创建swp文件，直接在内存中针对源文件编辑保存。  
> - 调用 **`:! vim -r`** 命令可以列举查看当前的 swap files。

### 使用 vim 阅读
#### 只读，可强制编辑，可强制写入（-R）
`vim -R` 以只读方式打开 `/usr/share/vim/vim73/tutor/tutor.utf-8`：

```Shell
➜  ~  cd /usr/share/vim/vim73/tutor
➜  tutor  vim -R tutor.utf-8
```

当 vim 以只读方式打开文件时：

- 若尝试通过`i`、`a`、`o`进入编辑模式，会提示：  
	> Warning: Changing a readonly file
- 编辑后，若尝试通过`:q`退出，会提示：  
	> No write since last change (add ! to override)
- 编辑后，若尝试通过`:w`保存，会提示：  
	> 'readonly' option is set (add ! to override)

但是，还是可以通过 `:wq!` 强制写入保存退出。

#### 可编辑，不可强制写入（-m）
在使用 vim 打开文档时，若允许针对缓冲副本（*.swp 文件）进行编辑，以便边学习边实践，但禁止最终写入保存，可以追加 `-m` 选项。

若尝试通过 `:w` 写入文件，会提示：

> File not written: Writing is disabled by 'write' option 

例如，以下通过 `vim -m` 命令对照阅读英文版 vimtutor（`tutor.utf-8`），可边阅读边实践：

```Shell
➜  ~  vim -m /usr/share/vim/vim73/tutor/tutor.utf-8
```

#### 只读，不可编辑，不可强制写入（-M）
如果仅仅使用 vim 打开文档进行阅读（类似 `less` 命令），可以追加 `-M` 禁止编辑修改。
若尝试通过 `i`、`a`、`o` 进入编辑模式，会提示：

> Cannot make changes, 'modifiable' is off

例如，以下通过 `vim -M` 命令以只读方式（不可编辑）对照阅读英文版vimtutor（`tutor.utf-8`）：

```Shell
➜  ~  vim -M /usr/share/vim/vim73/tutor/tutor.utf-8
```

[^cat]: The **[cat](https://en.wikipedia.org/wiki/Cat_(Unix))** (short for “concatenate“) command reads one or more files and prints them to standard output.
