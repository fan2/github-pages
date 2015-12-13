title: vim 学习 005 - vim 光标
date: 2015-12-13 14:13:40
categories: vim
tags: [vim-motion, vim-scroll]
---

本文介绍了 vim 中光标的移动及定位操作。

<!--more-->

光标定位可参考：**`/usr/share/vim/vim73/doc/motion.txt`**
可通过 `:help h` 或 `:h CTRL-N` 搜索帮助。

---

移动光标
----
※※ 要移動光標，請依照說明分別按下 `h`、`j`、`k`、`l` 鍵。 ※※

             ^
             k              提示︰ h 的鍵位于左邊，每次按下就會向左移動。
       < h       l >               l 的鍵位于右邊，每次按下就會向右移動。
             j                     j 鍵看起來很象一支尖端方向朝下的箭頭（down arrow）。
             v

在 Normal Mode 下，你当然也可以直接通过方向键（◀️🔽🔼▶️）来实现光标的移动，但 vi/vim 的宗旨是手指尽量集中在键盘中间排，以最小幅度的按键操作完成大部分定位及编辑工作。

按键        | 说明  | 备注
-----------|------|--------
`h`          |  ⬅️  | `<Left>`
`j` / `<C-n>`  |  ⬇️  | `<Down>`，next
`k` / `<C-p>`  |  ⬆️  | `<Up>`，previous
`l`          |  ➡️  | `<Right>`

在 Insert Mode 下，按下 **`hjkl`** 将输入普通的字母，此时只能通过方向键来移动光标，或 <Esc> 切回 Normal Mode 定位光标再切换回继续编辑。

位置回溯
----

按键        | 说明            | 备注
-----------|----------------|--------
`<C-o>`      | 光标回到上次位置  | takes back to older positions
`<C-i>`      | 光标回到下次位置  | takes to newer positions

vim 打开文件之后，光标一般都在文件开头，想要跳到上次关闭文件前的位置可按下 `'0` 。

单词定位
----

按键            | 说明
---------------|----------------
`b` / `<S-Left>`   | 移动到上一单词首
`w` / `<S-Right>`  | 移动到下一单词首
`e`              | 移动到当前单词尾
`ge`             | 移动到前一个单词尾

行内定位
----

按键           | 说明                | 备注
--------------|--------------------|--------
`0`	/ `|` / `Home`  | 移到行首            | "hard" BOL (Begin of Line)
`^` / `_` / `End`   | 移到行首（非空白字符） | "soft" BOL (End of Line)
`$`             | 移到行尾            | EOL
`{n}␣`          | 向右（下）移动n个字符 | 数字+空格
`%`             | 跳到匹配的括号处     | MATCHING PARENTHESES SEARCH: <br/>"{ }"、"[]"、"()"

行间定位
----

按键          | 说明         | 备注
-------------|--------------|--------
`-`            | 移动到上一行首 | 非空白字元
`+`            | 移动到下一行首 | 非空白字元
`{n}<CR>`      | 向下移动n行   | 数字+回车

行跳转定位
----

按键          | 说明           | 备注
-------------|----------------|--------
`gg`           | **移到文件第一行首** | 第一行 BOL：<br/>move to the start of the file
`G`            | **移到文件末尾行首** | 最后一行（EOF）的 BOL：<br/>move to the bottom of the file
`{n}gg` / `{n}G` / `:{n}` | 移到第n行     | n为行数

vim在打开文件时，光标默认定位在第一行行首，实际上可通过 `+` 带参数可以跳转到指定行。

```Shell
➜  ~  vim --help
usage: vim [arguments] [file ..]       edit specified file(s)

Arguments:
   --			Only file names after this
   
   +			Start at end of file
   +<lnum>		Start at line <lnum>
```

1. `+<num>`指定打开时光标所在行：  
	> ➜  ~  vim +39 /usr/share/vim/vim73/doc/motion.txt
2. `+`打开文件时光标定位到最后一行，方便append编辑：  
	> ➜  ~  vim + /usr/share/vim/vim73/doc/motion.txt

句段定位
----

方便以句、段为单位回溯/推进阅读：

按键         | 说明        | 备注
------------|-------------|--------
`(`           | 移到上一句首  | sentences backward
`)`           | 移到下一句首  | sentences forward
`{`           | 移到上一段首  | paragraphs backward
`}`           | 移到下一段首  | paragraphs forward
]]          | 移到下一句首{ | [count] sections forward or to the next '{' in the first column.
[[          | 移到上一句首{ | [count] sections backward or to the previous '{' in the first column.

][          | 移到下一句首} | [count] sections forward or to the next '}' in the first column.
[]          | 移到上一句首} | [count] sections backward or to the previous '}' in the first column.

屏显定位
----

按键          | 说明
-------------|--------------------
`H` (`<S-h>`)    | 移到当前屏幕的第一行 
`M` (`<S-m>`)	 | 移到当前屏幕的中间一行
`L` (`<S-l>`)	 | 移到当前屏幕的最后一行

**说明：**

> 这三个键控屏移仅针对当前屏幕显示范围，方便当前显示区域阅读，而非全文定位。

---
滚屏定位可参考：**`/usr/share/vim/vim73/doc/scroll.txt`**
可通过 `:help CTRL-D` 或 `:h CTRL-U` 搜索帮助。

滚屏定位
----

按键          | 说明               | 备注
-------------|-------------------|--------
`<C-e>`        | 屏幕向下移动一行     | move forward one-line
`<C-y>`        | 屏幕向上回滚一行     | move backward one-line
`<C-d>`        | 屏幕向下移动半页     | move **d**own half-page
`<C-u>`        | 屏幕向上回滚半页     | move **u**p half-page
`<C-f>`/`PgDn`	 | 屏幕向下滚动一页     | **f**orward
`<C-b>`/`PgUp`	 | 屏幕向上回滚一页     | **b**ackward
