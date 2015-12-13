title: vim 学习 007 - vim 编辑
date: 2015-12-13 14:26:40
categories: vim
tags: [vim-undo, vim-redo, vim-repeat, vim-change, vim-copy, vim-delete]
---

本文梳理了 vim 的基本编辑操作。

<!--more-->

撤销、重做
----
### 撤销（undo previous actions）
参考：`/usr/share/vim/vim73/doc/undo.txt`

按键           | 说明        | 备注
--------------|-------------|-----------------------------------
`u`             | 撤销（undo） | 直到`Already at oldest change`
`U`             | 撤销（undo） | 撤销针对光标所在行的操作：<br>undo all the changes on a line

**Undo branches**：                                *undo-branches* *undo-tree*

Above we only discussed **one line** of undo/redo.  But it is also possible to **branch off**.  
This happens when you undo a few changes and then make a new change.  The undone changes become a branch.  
You can go to that branch with the following commands.

按键               | 说明
------------------|---------------------------------------------
`:undol[ist]`       |   List the leafs in the tree of changes.
`g-` / `:ea`/`:earlier` | Go to older text state.
`g+` / `:lat`/`:later`  | Go to newer text state.

**`:undol[ist]` Example**:

number | changes | time           | saved
------:|--------:|---------------:|-----------------
 8     | 8       | 06:30:15
10     | 9       | 06:30:30
11     | 9       | 06:30:34
12     | 9       | 06:30:37

The "number" column is the change number.  
This number continuously increases and can be used to identify a specific ***undo-able*** change

### 重做（redo = undo the undo's）

按键           | 说明        | 备注
--------------|-------------|-----------------------------
`<C-r>`         | 重做（redo） | 直到 `Already at newest change`

重复（repeat last change/cmd）
----
参考：`/usr/share/vim/vim73/doc/repeat.txt`

按键           | 说明             | 备注
--------------|-----------------|--------
`.`             | 重复执行刚才的命令 | 不包括`undo`和`redo`命令

**说明：**  
若先按下数字（1\~9），再按下`.`，可重复执行指定次数。

修改（change）
----
参考：`/usr/share/vim/vim73/doc/change.txt`

### 替换字符（replace char）
按键           | 说明             | 备注
--------------|-----------------|--------
`r`	            | 替换光标所在的字符 | replace char，只替换一次<br/>替换后恢复指令模式
`R`             | 进入REPLACE MODE | 连续修改替换，`<Esc>` 退出。

### 复制（copy）

按键           | 说明                   | 备注
--------------|------------------------|--------
`y`             | 按下`→`，向右复制一个字符   | copy char forward
              | 按下`←`，向左复制一个字符 | copy char backward              
              | 按下`w`，向右复制一个单词 | copy word forward，包括空格
              | 按下`e`，向右复制一个单词 | copy word forward，不包括空格
              | 按下`b`，向左复制一个单词 | copy word backward
`y0`            | 复制到行首              | copy to BOL
`y$`            | 复制到行末              | copy to EOL
`Y`/`yy`          | **复制本行**                | copy/Yank line<br/>按`p`会插入粘贴到新起下一行
`y1G`           | 复制本行到首行           | copy to BOF
`yG`            | 复制本行到末行           | copy to EOF

**说明：**  
若先按下数字（1\~9），再按下 `y`、`Y` 或 `yy`，则表示复制指定数量的字符、单词或行。

### 粘贴（paste/put back）

按键           | 说明        | 备注
--------------|-------------|--------
`p`             | 向**后**粘贴     | paste forward<br/>在当前光标所在字符的后面粘贴
`P`（`<S-p>`）     | 向**前**粘贴     | paste backward<br/>在当前光标所在字符的前面粘贴
`:r [filename]` | 在当前光标所在行的下面读入filename文档的内容
`:r !date`      | 将`date`命令执行结果（当前日期）写入当前vim光标处 | reads the output of the `date` command and puts it below the cursor

**说明：**  
1. `P`和`p`的区别，相当于`i`和`a`。
2. `y`和`p`的复制粘贴只针对vim编辑器有效，`y`（包括下文中的`d`剪切操作）复制的内容不在系统剪切板中，在vim外无法使用`<C-v>`进行粘贴。
3. 若先按下数字（1\~9），再按下`p`或`P`，则表示重复粘贴指定次数。

> `2yy`：复制两行。

### 删除（delete）
#### d/c（delete motion/object）

按键           | 说明              | 备注
--------------|------------------|--------
`d`             | 按下`l`，相当于`x`     | delete char forward
              | 按下`h`，相当于`X` | delete char backward/backspace
              | 按下`w`，<br/>向右删除一个单词 | delete word forward，<br/>不包括下一个单词首字，删除空格
              | 按下`e`，<br/>向右删除一个单词 | delete to the end of the word，<br/>包括下一个单词首字，保留词间空格
              | 按下`b`，<br/>向左删除一个单词 | delete word backward
              | 按下`j`，<br/>删除本行及下一行 | delete current line and below
              | 按下`k`，<br/>删除本行及上一行 | delete current line and above
`d0`            | 删除至行首        | delete to BOL
`D`/`d$`          | 删除至行末        | delete to EOL
`dd`	        | **删除本行**          | delete line，下行上移
`dgg` / `d1G`	  | 删除本行到首行     | delete current line to first line
`dG`	        | 删除本行到末行     | delete current line to last line

`d*` 这种语法格式称为：`operator + motion` / `operator + object`。

**删除后编辑：**

> 与`d`、`D`/`d$`、`dd`、`dG`对应的有`c`、`C`/`c$`、`cc`/**`S`**、`cG`。
> `c`命令相对`d`命令不同的是**删除后进入insert模式**，相当于**修改**（ `c` 意即 ***change*** ）。

**说明：**

- 实际上这里的删除都是 `delete [into register x]`，等效于粘贴，可使用 `p`/`P` 粘贴回来。
- 在**operator**和**motion**之间或之前增加数字（prepend motion with a number:）：
	`d [number] motion`或`[number] d motion`

	> `d2w`或`2dw`：删除光标所在（之后）的两个单词（word）。
	> `d2d`或`2dd`：删除两行。

- 组合命令效果：

	> `ddp` 相当于交换两行的位置。
	> `dt.` 表示删除当前位置到下一个句号之间的内容。

- 可按键 `v` 进入可视模式（Visual Mode），针对选定内容作复制、删除操作。

#### x/s（delete char）

按键       | 说明            | 备注
----------|----------------|--------
`x`         | 向右删除一个字符(`dl`)  | delete char forward
`X`（`<S-x>`） | 退格删除一个字符(`dh`) | delete char backward/backspace
`s`         | 向右删除一个字符(`cl`)，<br/>并进入编辑模式 | delete char forward and enter inset mode
`S`         | 删除本行(`cc`)，并进入编辑模式 | Delete [count] lines and start insert.

**说明：**

- 实际上这里的删除都是 `delete [into register x]`，等效于粘贴，可使用 `p`/`P` 粘贴回来。
- 若先按下数字（1\~9），再按下 `x`/`X` 或 `d`/`dd`，则表示删除指定数量的字符、单词或行。
- 组合命令效果：`xp` 相当于交换两个字符的位置。
- 可按键 `v` 进入可视模式（Visual Mode），针对选定内容作复制、删除操作。

行缩进
----
The ">" and "<" commands are handy for changing the indentation within
programs.  
Use the '**shiftwidth**' option to set the size of the white space which these commands insert or delete.  Normally the 'shiftwidth' option is 8.

指令       | 说明
----------|-----
`<`{motion} | Shift {motion} lines one 'shiftwidth' leftwards.
`<<`        | Shift [count] lines one 'shiftwidth' leftwards.
{Visual}[count]`<` | Shift the highlighted lines [count] 'shiftwidth' leftwards.
`>`{motion} | Shift {motion} lines one 'shiftwidth' rightwards.
`>>`        | Shift [count] lines one 'shiftwidth' rightwards.
{Visual}[count]`>` | Shift the highlighted lines [count] 'shiftwidth' rightwards.

命令        | 说明
-----------|-----
`:[range]<`  | Shift [range] lines one \'shiftwidth\' left. <br/>Repeat `<` for shifting multiple \'shiftwidth\'s.
`:[range]< {count}` | Shift {count} lines one \'shiftwidth\' left, starting with [range].<br/>Repeat `<` for shifting multiple 'shiftwidth's.
`:[range]le[ft] [indent]` | left align lines in [range].  Sets the indent in the lines to [indent] (default 0).
`:[range]> [flags]` | Shift {count} [range] lines one 'shiftwidth' right.<br/>Repeat `>` for shifting multiple 'shiftwidth's.
`:[range]> {count} [flags]` | Shift {count} lines one 'shiftwidth' right, starting with [range].<br/>Repeat `>` for shifting multiple 'shiftwidth's.

**说明：**

> See |ex-flags| for [flags].

行合并
----

按键           | 说明                | 备注
--------------|---------------------|--------
`J`             | 合并两行（Join Lines）| 将下一行合并到当前行。

保存退出（save & exit）
----
Last Line Mode：

命令            | 说明        | 备注
---------------|-------------|--------
`:q`             | 退出（quit） | 如果无修改直接退出；<br/>否则提示`No write since last change (add ! to override) `
`:q!` / `ZQ`       | 强制退出     | 放弃修改
`:w`/`:write` / `:up`/`:update`	| 写入（write）保存 | 从swap buffer写入文件；<br/>若以只读方式打开，则提示'readonly' option is set (add ! to override)；<br/>也可另存为：`:w [filename]`
`:w!`          | 若文本属性为只读时，强制保存
`:wq` / `ZZ` / `:x`/`:exit` | 保存并退出
`:wq!`         | 强制保存并退出

其他
----
命令                 | 说明
--------------------|-----------------------
`:n1,n2 w [filename]` | 将n1到n2的行内容另存为文档
