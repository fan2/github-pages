title: svn 版本控制
date: 2015-11-27 14:36:30
categories: svn
tags: [svn-info, svn-log, svn-revert, svn-diff, svn-patch, svn-merge]
---

之前介绍过 [subversion 版本控制](http://blog.csdn.net/phunxm/article/details/40834427)，本文梳理日常 svn 操作流程，备忘查阅。

<!--more-->

## svn auth-cache
一般第一次操作过 svn 后（没有指定 `--no-auth-cache`），会提示确认 auth cache，后面的 svn 操作都会使用缓存的 auth token。

1. linux/Mac 将 auth-cache 保存在 `~/.subversion/auth` 目录下，可执行 `rm -rf ~/.subversion/auth` 命令清除。
2. 在 Windows TortoiseSVN 下通过以下步骤可以[清除缓存的 svn 认证信息](http://www.justwinit.cn/post/4512/)，下次就会要求输入用户名和密码：

> 1. 在任一 Working Copy 上右键，选择 TortoiseSVN-->settings；
> 2. 在弹出的 TortoiseSVN Settings 页面中选择 **`Saved Data`** 选项； 
> 3. 点击 **`Authentication data`** 对应的 **Clear** 按钮。

终端 `svn checkout --help` 或 `svn commit --help` 可看到 **_Global options_** （适用于 svn 大部分 subcommands）中通过 `--username`/`--password` 选项可指定用户名和密码：

```Shell
Global options:
  --username ARG           : specify a username ARG
  --password ARG           : specify a password ARG
```

如果想临时使用别人的账号 checkout 或 commit 代码，而不使用缓存的 auth token，通过指定 `--username`/`--password` 参数进行 svn 操作即可。

```Shell
$ svn co URL --username user --password pwd
$ svn ci URL --username user --password pwd
```

如果可以进入 svn 服务器的话，在 `***\conf\authz` 中可根据用户名查询 [svn 权限](http://www.cnblogs.com/perfy/p/3508210.html)。

## svn ignore
**[SmartSVN 设置忽略文件类型设置](http://blog.csdn.net/qw503495315/article/details/17454625)**  

在左侧 Directories Explorer中右击 .git 文件夹选择 ignore。
在 Files Explorer 中右击 .gitignore 文件选择 ignore | Ignore Explicitly，则所有目录的同名都将被忽略。
右键根目录，在 Properties | Ignore Patterns 中可以删除或编辑 Ignore Patterns。

**注意：**

unversioned 的文件（目录）需要先添加到 svn 版本库，才能忽略。  
SmartSVN 已经帮我们将要忽略的 unversioned 文件（目录）添加到版本库，谨防切换到命令行操作时错误提交！

**参考：**

> 《[svn 设置忽略文件列表](http://blog.chinaunix.net/uid-10167808-id-83417.html)》《[SVN 设置忽略文件列表](http://my.oschina.net/shelllife/blog/142257)》  
> 《[SVN 忽略整个目录](http://huanyue.iteye.com/blog/750108)》《[Ignoring Unversioned Items](http://svnbook.red-bean.com/en/1.8/svn.advanced.props.special.ignore.html)》  
> 《[SVN忽略文件或文件夹几种方法总结](http://www.111cn.net/sys/linux/53797.htm)》《[给SVN控制的项目添加忽略文件/文件夹](http://yansu.org/2013/04/22/add-svn-ignore-file.html)》  

## svn info
`svn info` 命令可以查看当前工作目录（Working Copy）的版本信息。

```Shell
⇒  svn info --help
info: Display information about a local or remote item.
usage: info [TARGET[@REV]...]
```

通过 `--show-item` 指定选项，可以打印指定项。

     kind                  the kind of TARGET
     url                   the URL of TARGET in the repository
     relative-url          the repository-relative URL
     repos-root-url        the repository root URL
     repos-uuid            the repository UUID
     revision              the revision of TARGET (defaults to BASE
                           for working copy paths and HEAD for URLs)
     last-changed-revision the most recent revision in which TARGET
                           was changed
     last-changed-date     the date of the last-changed revision
     last-changed-author   the author of the last-changed revision
     wc-root               the root of TARGET's working copy

以下示范 svn checkout Reachability 工程的 info：

```
faner@MBP-FAN:~/Projects/git/Reachability|
⇒  svn info
Path: .
Working Copy Root Path: /Users/faner/Projects/git/Reachability
URL: https://github.com/tonymillion/Reachability
Relative URL: ^/
Repository Root: https://github.com/tonymillion/Reachability
Repository UUID: 97ad356b-402e-f69d-e5e9-ce57b7db7bff
Revision: 89
Node Kind: directory
Schedule: normal
Last Changed Author: tony.million
Last Changed Rev: 89
Last Changed Date: 2015-09-12 00:42:38 +0800 (六, 12  9 2015)
```

## svn status
[status](http://blog.linuxphp.org/archives/1377/) (stat, st): 显示工作副本（Working Copy）中的目录与文件状态。

执行 `svn status` 或 `svn up`（`svn merge`）等命令时，出现在首位置的标志含义如下表：

标志 | 状态
----|-----------
“ ” | 无修改
A   | 增加（Added）
D   | 删除（Deleted）
I   | 忽略（Ignored）
M   | 改变（Modified）
[MM](http://stackoverflow.com/questions/10180700/what-does-mm-means-when-i-run-a-svn-diff)  | 文件的属性和内容都被改变了
U   | 表示更新（Updated）
UU  | 文件的属性和内容都更新了
C   | 冲突（Conflicted）
G   | 合并（Merged）
E   | 已经存在（Existed）
R   | 替换（Replaced）
X   | 未纳入版本控制的目录，被外部引用的目录所创建
?   | 未纳入版本控制，需要执行 `svn add`
!   | 该项目已遗失(被非 svn 命令删除)或不完整
~   | 版本控制下的项目与其它类型的项目重名

执行 `svn status` 没有指定选项参数时，默认只显示本地修改条目的摘要信息（`-q`）。   也可以指定 `-v` （`--verbose`）选项，显示每个条目的完整版本信息。

## svn add

```Shell
faner@MBP-FAN:~|⇒  svn add --help
add: Put files and directories under version control, scheduling them for addition to repository.
They will be added in next commit.

usage: add PATH...
```

`svn add` 支持添加单个**文件**到版本控制，同时亦支持添加**文件夹**，它将自动将文件夹下未纳入版本控制的文件添加到 svn 版本控制。

```Shell
$ pwd
~/Projects/branch/Classes/module/FAUI/FAView
$ svn add FAViewBottom.h FAViewBottom.m
A         FAViewBottom.h
A         FAViewBottom.m
```

执行完 `svn add` 将文件（夹）添加到本地 WC（Working Copy）之后，还需要执行 commit 命令提交到服务器：

> $ svn commit -m 'add FAViewBottom'

## svn commit 

```Shell
faner@MBP-FAN:~|⇒  svn commit --help
commit (ci): Send changes from your working copy to the repository.
usage: commit [PATH...]

  A log message must be provided, but it can be empty.  If it is not given by a --message or --file option, an editor will be started.

Valid options:
  -q [--quiet]             : print nothing, or only summary information
  --depth ARG              : limit operation by depth ARG ('empty', 'files', 'immediates', or 'infinity')
  -m [--message] ARG       : specify log message ARG
  -F [--file] ARG          : read log message from file ARG
  --editor-cmd ARG         : use ARG as external editor
```

执行 `svn checkout` 将代码下载到本地进行增删改后，如果需要上库同步修改成果，可执行 `svn commit` 命令提交修改。

`svn commit` 支持提交单个**文件**，同时亦支持提交**文件夹**，它将自动收集提交文件夹下有改动的文件。

参数 `-m [--message]` 指定此次提交的日志，或通过 `-F [--file]` 选项指定从文件中读取日志。

> $ svn ci test.m -m "This is log message"
> $ svn ci test.m -F log.txt

`svn commit -m `提交代码时，通常只能写一行日志信息。如果想要书写**多行日志**，可以通过 `$' '` 格式支持换行。  
`$' '`：单引号引用的内容中支持使用转义字符 `\n` 进行换行，可 echo 查看效果：

```Shell
$ echo $'This is the first line\nThis is the second line'
This is the first line
This is the second line
```

> $ svn ci test.m -m $'This is the first line\nThis is the second line'

`svn commit` 提交代码时，可通过 `-editor-cmd` 参数选项指定 log 编辑器。例如，以下提交代码时将打开 `vim` 撰写日志（`:wq` 保存退出提交）。

> $ svn ci test.m --editor-cmd=vim

## svn delete

```Shell
faner@MBP-FAN:~|⇒  svn delete --help
delete (del, remove, rm): Remove files and directories from version control.
usage: 1. delete PATH...
       2. delete URL...

```

`svn delete` 支持将单个**文件**移除版本控制，同时亦支持删除**文件夹**，它将自动递归将文件夹下的文件解除版本控制。

```Shell
$ pwd
~/Projects/branch/Classes/module/FAUI/FAView
$ svn delete FAViewBottom.h FAViewBottom.m
D         FAViewBottom.h
D         FAViewBottom.m
```

执行完 `svn delete` 将文件（夹）从到本地 WC（Working Copy）中解除版本控制之后，还需要执行 commit 命令提交到服务器：

> $ svn commit -m 'delete FAViewBottom'

## [svn log](http://www.nc21.cn/ncnet/article.asp?nc=15-12-147-0-6735.xhtml)
- 显示当前 svn 工程的 log（till base of the working copy）

> $ svn log

- 显示当前 svn 工程中指定文件的 log

> $ svn log FACategoryTableView.m

- 显示当前 svn 工程中指定文件某个 revision 的 log

> $ svn log FACategoryTableView.m@v #v必须≤base的版本号

- 显示服务器上某个文件的 log（HEAD will be first looked up：@HEAD means range HEAD:1）

> $ svn log path/to/your-svn-repo/directory/file.c

- 显示服务器上某个文件某个 revision 以及之前的 log（REV will be first looked up：@REV=REV:1）

> $ svn log path/to/your-svn-repo/directory/file.c@113629

- 显示服务器上某个文件在某几个 revision 之间（`-r`（`--revision`）选项）的变更日志

> $ svn log path/to/your-svn-repo/directory/file.c -r 113714:113629

- 查看最近 N 条log，`-l` （`--limit`）选项指定 limitation，后面的 N 为数字

> $ svn log -l N

>> $ svn log -l 3

- 日志重定向到文件

> $ svn log -r 14 > svn.log

**注意**:

> 当你执行 svn commit 提交后，svn log 并不能看到刚才提交的版本日志。
需要执行 **update** 以后，才能看到最新的 log，因为 commit 和 update 是独立的操作，commit 并不更新本地版本信息！

## svn revert

```Shell
⇒  svn revert --help
revert: Restore pristine working copy state (undo local changes).
usage: revert PATH...

  Revert changes in the working copy at or within PATH, and remove
  conflict markers as well, if any.

  This subcommand does not revert already committed changes.
  For information about undoing already committed changes, search
  the output of 'svn help merge' for 'undo'.

```

1. 恢复文件：

	> svn revert test.mm

2. 恢复文件夹（--recursive）：

	> svn revert -R .

3. **Roll back (Undo)**

`svn revert` 没有 `-r ` 选项来支持回滚到指定版本号，只能通过间接方式手动实现回滚。

> TortoiseSVN → Show log → 选中需要回滚的版本 → 右键 → Export（命令行 `svn checkout -r  `）

通过以上操作，直接 export 一个你需要的版本，然后用 export 的版本覆盖最新版本，commit 即实现了等效的回滚。

## svn diff
`svn diff` 命令用于查看当前目录下的改动（依次 svn diff 各个有改动的文件的差异）：

	```Shell
	$ svn diff -r newversion:oldversion path
	$ svn diff -r r1:(r1-1)  (filename)
	```

1. 不使用任何参数时，svn diff 将会比较 working copy 与缓存在 `.svn` 中的“原始”拷贝：

	> $ svn diff

2. 只传递一个 `--revision`（-r）参数时，工作拷贝将会与指定的版本比较：

	> $ svn diff -r 108249 FACategoryTableView.m
	
	默认追加:HEAD!

3. 通过 `--revision`（-r）传递两个通过冒号分开的版本号，会对这两个版本进行比较：

	> $ svn diff -r 108314:108249 FACategoryTableView.m

4. diff 不显示各个文件的详细差异比较，**只显示增删改概要**，以便作为commit参考：

	```
	$ svn diff --summarize
	```

## svn patch（打补丁）
### TortoiseSVN（windows）
1. 项目右键 `TortoiseSVN|Create patch...`
2. 选择改动要打 patch 的文件，将会生成 `*.patch`，同时弹出 TortoiseUDiff 显示 diff 。
3. 将 `*.patch` 拷贝到其他分支同级别目录下，右键 `TortoiseSVN|Apply patch...`，选择 `Patch selected item`，将会自动根据补丁中的 Index 路径合并 diff 到对应文件。

> [用 Tortoise SVN 抽取补丁包（patch）](http://blog.csdn.net/gaojinshan/article/details/7856550)  
> [如何让 TortoiseSVN 仅导出新增或修改过(变更过)的文件](http://www.uml.org.cn/pzgl/200903276.asp)

### 命令行（Mac OS X）
1. 执行 `svn diff --summarize` 查看有哪些文件改动

	```Shell
	⇒  svn diff -h
	diff (di): Display local changes or differences between two revisions or paths.
	
	⇒  svn diff --summarize
	M       path1/to/test.mm
	```

2. 对欲打补丁的改动文件执行 `svn diff` 并重定向保存为补丁（`*.diff` 或 `*.patch`），执行 `cat` 或 `vim` 可查看 diff 详情：

	```
	⇒  svn diff test.mm > test.diff
	
	⇒  cat test.diff
	
	```

3. 将补丁文件（test.diff）拷贝到其他分支同级别目录下，执行 `svn patch` 命令 将会自动根据补丁中的 Index 路径合并 diff 到对应文件。

	```
	⇒  svn patch --help
	patch: Apply a patch to a working copy.
	usage: patch PATCHFILE [WCPATH]
	
	⇒  svn patch test.diff 
	U         path2/to/test.mm
	```

## svn merge（同步 & 合流）

```Shell
⇒  svn merge --help
merge: Merge changes into a working copy.
usage: 1. merge SOURCE[@REV] [TARGET_WCPATH]
          (the 'complete' merge)
       2. merge [-c M[,N...] | -r N:M ...] SOURCE[@REV] [TARGET_WCPATH]
          (the 'cherry-pick' merge)
       3. merge SOURCE1[@REV1] SOURCE2[@REV2] [TARGET_WCPATH]
          (the '2-URL' merge)
```

### master2branch（merge to sync/rebase）
同步最新主干到分支（同步基线）：TortoiseSVN 基于 branch working copy 右键 merge

- URL to merge from：填写主干 master 的 svn url；
- Revision range to merge：选择 **`specific range`**。

	选择上次 rebase 的 rev（112603）作为起始点，最新的 rev 作为结束点。

    - 112608（one rev）
    - 112608,112630（two rev）
    - 112608-112630（range through）
    - 1-HEAD（rebase 到最新，自动识别起始）

- Test merge 为 dry-run 可预览合并结果；Merge 执行合并操作。
- 如果提示有冲突，本地手工解决。
- commit 提交本地分支 WC 的 rebase 合并结果。

**同步结果：**

> Merging revisions 1-HEAD of  `http://yoursvnserver/basic/basic_your_rep/your_proj/trunk/master` into `path/to/your/local-branch-working-copy`
> Merging `rX` through `rY`

**svn 命令行：**

> cd `path/to/your/local-branch-working-copy`
> svn merge `http://yoursvnserver/basic/basic_your_rep/your_proj/trunk/master` .

**日志：**

> --- Merging differences between repository URLs into '.':

### branch2master（merge to reintegrate）
合并开发分支到主干（合流）：TortoiseSVN 基于 master working copy 右键 merge

branch2master（基本步骤同master2branch）

- URL to merge from：填写分支 branch 的 svn url；
- Revision range to merge：选择 **`all revisions`**。
- Merge options 中勾选 “Do reintegrate instead of automatic merge（old style）”，相当于旧版本的 Merge type："Reintegrate a branch"。
- Test merge 为 dry-run 可预览合并结果；Merge 执行合并操作。
- 如果提示有冲突，本地手工解决。注意 **revert** 本地未做修改的 property only 的 change/modification。
- commit 提交本地主干 WC 的 rebase 合并结果。

**合流结果：**

> Merging revisions of 1-HEAD of
`http://yoursvnserver/basic/basic_your_rep/your_proj/branches/BusinessFlow/branch` into `path/to/your/local-master-working-copy`
> Merging `rX` through `rY`

**svn 命令行：**

> cd `path/to/your/local-master-working-copy`
> svn merge --reintegrate  `http://yoursvnserver/basic/basic_your_rep/your_proj/branches/BusinessFlow/branch` .

**日志：**

> --- Merging differences between repository URLs into '.':

### Merge two different trees
Tree merge

- From：（start URL and revision of the range to merge）

    “起始URL和版本”一般情况下必须是开发分支创建的起始版本。
    主干的 URL、版本为 HEAD

- To：（end URL and revision of the range to merge）

    “结束的URL和版本”是此次操作的最终标准。
    分支的 URL、版本为 HEAD
    
- Working Copy

    From：（start URL and revision of the range to merge）


