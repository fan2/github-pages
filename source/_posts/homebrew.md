title: Mac 下的包管理工具 —— brew
date: 2015-11-22 15:05:00
categories: Mac
tags:
- DPKG
- APT
- RPM
- SRPM
- YUM
- MacPorts
- Homebrew
- brew
- brew-cask
---

本文前驱铺垫梳理了从 linux 源码安装软件到 DPKG/RPM 软件包管理机制，阐述了 *UNX 平台的软件安装包管理机制的演变和现代化。  
后半部分着重介绍 Mac 下的软件安装管理工具 —— brew 及 brew-cask，梳理日常  Command Usage。

<!--more-->

## [linux 软件的安装与管理](http://blog.chinaunix.net/uid-509190-id-3056219.html)
[linux](https://github.com/torvalds/linux) 是一套免费使用和自由传播的类Unix操作系统，是一个基于 POSIX 和 UNIX 的多用户、多任务、支持多线程和多CPU的开源操作系统。  
由于 Linux 操作系统开放源代码，因而在其上安装的软件大部分也都是开源软件。开源软件开发者往往仅需在开源代码托管平台上发布一份源码包，用户可以下载源码包到本地，基于源码包编译安装软件。这倒是非常符合 C 语言的设计哲学：一次编写，到处编译。

### [基于源码安装软件](http://www.cnblogs.com/huangfenghit/archive/2011/02/17/1957057.html)
基于源码安装软件一般由以下几个步骤组成：

- 下载解压源码
- 阅读 README、INSTALL 等说明文档，这一步至关重要
- 分析安装平台环境（ifconfigure）
- 编译安装软件（make，make install）

基于源代码安装软件的好处是：用户可以自由配置编译选项，按需编译实现功能定制，极大地满足**[个性化需求](http://www.zhihu.com/question/21949923)**。此外，用户还可以自己选择安装路径，方便管理。卸载软件也很方便，只需删除对应的安装目录即可。  
但是，配置、编译命令需要了解操作系统本身，并且依赖开源项目所使用的编程语言对应的工具链。为了使用一个应用软件，用户需要熟悉 linux 系统的文件组织架构和一堆  Shell 交互命令，还得解决编译过程中可能涉及到的繁杂的依赖关系。一定的英文水平也是必需的，关键要有折腾不息的精神和顽强的动手能力！  
English、OS、Linux、命令行交互方式、...，这些令人望而却步的门槛足以将普通用户拒之门外。安装后，你可能都不知道[安装到哪里去了](http://www.linuxidc.com/Linux/2009-11/22842.htm)；甚至离开了 Windows 桌面，你都不知道从哪里启动软件。我依稀地记得刚接触 linux 那会儿，为了能在 linux 上播放个视频，废了老大劲才安装好一个 [MPlayer](http://blog.pfan.cn/xman/41044.html)。当时就森森地觉得 linux 真不是一般人能折腾得起，尽管现在已经折腾惯了，但依然觉得费脑伤神。  
作为一个曾经的[计算机旁系学生](http://www.tinylab.org/why-computer-students-learn-linux-open-source-technologies/)（自动化，别名计算机控制）、现在半职业化软件攻城狮（涉猎甚广，浅显浮泛）和伪自由软件追崇者（用过盗版，不懂破解，从无贡献），因为工作关系经常穿梭于 Windows 和 Mac OS X 之间（游离其间，无所专精），不折腾下 Linux/Unix 都觉得没法混 github。  
尽管对于 vim 键盘型用户(基本都是程序员吧)，很喜欢那种游离于指尖的"自由感"，但对于普通鼠标型 Windows 用户，[若无力驾驭，自由便是负担。](http://www.zhihu.com/question/20117703)  

> 只有开发人员才需要学习一个操作系统本身，用户只需要学习操作系统里面的每个“应用程序”怎么使用。 —— Linus Torvalds

### DPKG / RPM
伴随着 linux 的发展普及，linux 开发商开始在固定的硬件平台与操作系统上将要安装或升级的软件**编译好**，然后将这个软件的所有相关软件**打包**成为一个特殊格式的文件。在这个软件内，还包含了预先检测系统与依赖软件的脚本，并提供记载该软件提供的所有文件信息等，最终将这个软件发布。  
客户端取得软件后，只要通过特定的命令来安装，那么该软件就会按照内部的脚本来检测相关的前驱软件是否存在。若安装的环境和条件符合要求，则开始安装。软件在安装完成后，还会将信息写入软件管理机制中，以便完成未来的升级、删除(反安装)等操作。

目前，在 Linux 界最常见的软件安装方式有两种：

1. DPKG

	- **DPKG**（Debian Packager）是由 Debian Linux 社区所开发出来的，著名的 `package` 概念开始出现在 GNU/Linux 系统中。
	- 通过 DPKG 机制，Debian 提供的软件就能够简单安装起来，同时还能提供安装后的软件信息。派生于 Debian 的其他 Linux Distributions 大多使用 dpkg 机制来管理软件，包括 B2D、Ubuntu 等。
	- Debian 为解决软件包更新问题，引入了 APT（Advanced Package Tool）[在线升级机制](http://www.cnblogs.com/kulin/archive/2012/07/31/2616490.html)，并在 `/etc/apt/sources.list` 文件列出了可获得软件包的镜像站点地址。
		- **APT** 由几个名字以 `apt-` 打头的程序组成，apt-get、apt-cache 和 apt-cdrom 是处理软件包的命令行工具。
		- apt 作为 dpkg 的前端工具，自动管理关联文件和维护已有配置文件，在于拥有出色的解决软件依赖问题的能力。

2. RPM

	-  **RPM**（RedHat Package Manager）是由 Red Hat 公司所开发出来的软件包管理程序。除了可以用来安装（-i）外，还可以进行查询（-q）、验证（-V）、更新（-U）、删除（-e）等操作，这些功能选项让软件的管理更加方便。包括 Fedora、CenterOS、SuSE 等知名的开发商都是使用 RPM 作为软件安装的管理机制。
	- RPM 包的封装格式一般有两种，分别是 **RPM** 和 **SRPM**。RPM 格式的文件 `xxx.rpm` 内含已经经过编译的二进制包和配置文件等数据。SRPM（Source RPM）对应的 RPM 文件类似于 `xxx.src.rpm` 格式，它包含了编译时的源码文件和一些编译指定的参数文件。因而，在使用的时候，需要先以 RPM 管理的方式编译为 RPM 文件(rpmbuild)，再将编译完成的 RPM 文件安装到 Linux 系统中。
	- RPM 无法自动解决软件的依赖关系，使用与 APT 对应的 **[YUM](http://www.cnblogs.com/mchina/archive/2013/01/04/2842275.html)** (Yellowdog Updater Modified) 机制可以**解决属性依赖问题**。**[YUM](http://www.cnblogs.com/chuncn/archive/2010/10/17/1853915.html)** 作为基于 RPM 的 Shell 前端软件包管理器，主要用于自动升级、安装/移除 RPM 软件包。它能够自动查找并解决 RPM 包之间的依赖关系，而无需管理员逐个手工的去安装每一个 RPM 包。

Distribution 代表 | 包管理机制 | 使用命令 | 在线升级机制(命令)
-----------------|-----------|---------|-------------------
Red Hat / Fedora | RPM       | rpm，rpmbuild | YUM(yum)
Debian / Ubuntu  | DPKG      | dpkg          | APT(apt-get)

#### RPM / SRPM
RPM 软件包命名规范：`name-version-release.arch.rpm`。其中，`release` 表示系统的发行版，如 fc18、el6 表明这个软件包是在 Fedora 18、RHEL 6.x / CentOS 6.x 下使用的；`arch` 表示硬件平台，常见的有 i386、x86_64 等。  
一个典型的 RPM 安装包命名格式为 `rp-pppoe-3.1-5.i386.rpm`，它由几个部分构成：

- `rp-pppoe`：名称
- `3.1`：版本信息
- `5`：发布次数
- `.i386`：适合的硬件平台
- `.rpm`：扩展名

RPM 文件必须要在相同的 linux 环境才能安装，而 SRPM 既然是源代码格式，我们就可以通过修改 SRAM 内的参数，按需编译生成适合我们 linux 环境的 RPM 文件，而不必与原作者打包的 Linux 环境相同。通常一个软件在发布的时候，都会同时释放出该软件的 RPM 与 SRPM。

文件格式 | 扩展名格式    | 直接安装与否 | 内含程序类型 | 可否修改参数并编译
--------|-------------|-----------|-------------|----------------
RPM     | `xxx.rpm`     | √         | 已编译       | ×
SRPM    | `xxx.src.rpm` | ×         | 未编译的源码  | √

## [Mac 软件包管理工具](https://github.com/pubyun/macdev/blob/master/basic.md)
linux 下的 apt-get 和 yum 命令行工具分别适用于 deb、rpm 包管理方式的发行版本，主要用于自动从互联网的软件仓库中搜索、安装、升级和卸载软件。  
在 Mac OS X 平台下，同样有优秀的软件包管理工具，可以管理大量 AppleStore 没有提供，而你又经常会用到的开源软件包。我们有两种选择 —— MacPorts 和 Homebrew。

**MacPorts** 是由 FreeBSD 的 port 移植而来；**[Homebrew](http://brew.sh/)**（简称 brew）是 Mac OS X 不可或缺的软件管理工具(The missing package manager for OS X)，[让 Mac 拥有类似 apt-get 的功能](http://snowolf.iteye.com/blog/774312)，用以简化软件的安装、升级和卸载过程。

> **Homebrew** is the easiest and most flexible way to install the UNIX tools.

Homebrew 是一款使用 Ruby 进行开发的托管于 [GitHub](https://github.com/Homebrew/homebrew) 上的自由及开放源代码的软件包管理系统，通过用户的贡献扩大对软件包的支持。

MacPorts 和 Homebrew 这两种包管理系统的工作方式都是下载源代码，然后在本地编译。但是这两种包管理系统还是存在很大差异的，主要差异有以下三点：

- MacPorts 的理念是尽量减少对系统现有库的依赖，编译时间较长，不怎么依赖系统；而 Homebrew 则是尽量依赖系统现有库，编译时间会显著减少，但与系统紧密依赖。
- MacPorts 的 Package 都是安装到 `/opt/local` ，不会与系统现有的软件发生冲突；而 Homebrew 的 Package 都是安装到 `/usr/local`，可能与系统自带的软件发生冲突。
- Macports 使用 rsync 进行同步（[也可以使用svn](https://trac.macports.org/wiki/howto/SyncingWithSVN)），而 Homebrew 使用 git 进行同步。

总体来说，Homebrew 使用简单，编译时间短，比较适合新手使用。MacPorts 编译时间长，命令还要带上 sudo，易用性上没有 Homebrew 好，但是比较干净，适合有洁癖的人使用。

以上梳理了 Linux 和 Mac 下的包管理机制，下面重点介绍 Mac OS X 上的 brew 及 brew-cask。

## brew (homebrew)
**brew** 是从软件包仓库下载源代码码到本地进行解压，进而执行 `./configure` && `make install` ，将软件编译安装到单独的目录（`/usr/local/Cellar`）下，然后软链（symlink）到 `/usr/local` 目录下，同时会包含相关依赖库，并自动配置好各种环境变量。  
这个对程序员来说简直是福音，使用简单的指令就能快速安装、升级和卸载本地的各种开发环境。

## brew -v / --version
查看本机安装的 brew 管理工具：

```Shell
faner@MBP-FAN:~|⇒  brew -v
Homebrew 0.9.5 (git revision 1eec1; last commit 2015-11-21)
faner@MBP-FAN:~|⇒  brew --version
0.9.5 (git revision 1eec1; last commit 2015-11-21)
```

## brew help
执行 `brew` 查看帮助概要（Example usage、Troubleshooting、Brewing）：

> -h / --h / --help / help

```Shell
faner@MBP-FAN:~|⇒  brew
Example usage:
  brew [info | home | options ] [FORMULA...]
  brew install FORMULA...
  brew uninstall FORMULA...
  brew search [foo]
  brew list [FORMULA...]
  brew update
  brew upgrade [FORMULA...]
  brew pin/unpin [FORMULA...]

Troubleshooting:
  brew doctor
  brew install -vd FORMULA
  brew [--env | config]

Brewing:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]
  https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Formula-Cookbook.md

Further help:
  man brew
  brew home
```

1. 执行 `man brew` 可查看详细的 brew 帮助文档。
2. 执行 `man home` 可调用浏览器打开 brew 官方网站。

**brew 常用命令：**

命令				      | 说明
-----------------------|---------------------
brew update            | 更新 brew
brew search FORMULA    | 查找软件包，可使用正则表达式
brew info FORMULA      | 显示软件的信息
brew deps FORMULA      | 显示包依赖
brew install FORMULA   | 安装软件包
brew uninstall FORMULA | 卸载软件包
brew list              | 列出已安装的软件包，可指定 FORMULA
brew outdated          | 列出可升级的软件包
brew upgrade           | 更新已安装的软件包，可指定 FORMULA
brew doctor            | 诊断 homebrew 环境
brew prune             | 删除 /usr/local 下的无效链接(remove broken symlinks)

以下示例查找 `cask` 包，并显示软件包信息和依赖关系：

```Shell
faner@MBP-FAN:~|⇒  brew search cask
cask
homebrew/completions/brew-cask-completion

faner@MBP-FAN:~|⇒  brew info cask
cask: stable 0.7.3, HEAD
Emacs dependency management
https://cask.readthedocs.org/
Not installed
From: https://github.com/Homebrew/homebrew/blob/master/Library/Formula/cask.rb

faner@MBP-FAN:~|⇒  brew deps cask
emacs
```

`brew info` 显示软件包信息，一般包括：

- 软件概要信息
- 本地是否已安装：Not installed / Poured from bottle
- 依赖包：==> Dependencies，Build、Required、Recommended、Optional
- 编译、安装选项： ==> Options
- 预警信息：==> Caveats

[brew info 查看 plist 文件](https://ruby-china.org/topics/21050)

## [brew cask](http://www.zhihu.com/question/22624898)
Homebrew 作为 Ruby 社区极富想象力的作品，使得 Mac 下安装 Mysql 等常用包不再困难。那么，是否也可以通过 `brew install mysql` 这样简单的方式来安装 Google Chrome 浏览器呢？为解决这一问题，phinze 的作品 [homebrew-cask](https://github.com/phinze/homebrew-cask) 应运而生。

### about
Homebrew 可以管理 Mac 下的命令行工具（wget、node），**brew cask** 则是一套建立在 brew 上的**增强**命令行工具，支持管理 Mac 下的 GUI 程序，例如 qq、 google-chrome、evernote 等。  
cask 从镜像源下载已经编译好了的[应用软件二进制包](https://github.com/phinze/homebrew-cask/tree/master/Casks)（.dmg/.pkg）到本地解压到单独的目录（`/opt/homebrew-cask/Caskroom`）下，然后软链（symlink）到 `/Applications` 目录下。  
cask 包含了很多在 AppStore 里没有的常用软件，省掉了手动下载、解压、拖拽（安装）等步骤，且卸载也相当容易与干净，使用起来非常方便。

### install
假设你已安装好了 Homebrew，安装与使用 cask 也极其简单，打开终端输入：

```Shell
brew tap phinze/homebrew-cask
brew install brew-cask
```

#### brew tap phinze/homebrew-cask

由于 brew 和包含的包源都是通过 github 来管理，人为的维护管理。除了默认的仓库，还允许别人的源添加进来。通过 [`brew tap`](http://icyleaf.com/2014/01/homebrew-hidden-commands/) 指定第三方包源：

> $ brew tap <gihhub_user/repo>

```
faner@MBP-FAN:~|⇒  brew tap phinze/homebrew-cask
==> Tapping phinze/cask
Cloning into '/usr/local/Library/Taps/phinze/homebrew-cask'...
remote: Counting objects: 3278, done.
remote: Compressing objects: 100% (3202/3202), done.
remote: Total 3278 (delta 83), reused 731 (delta 59), pack-reused 0
Receiving objects: 100% (3278/3278), 5.82 MiB | 428.00 KiB/s, done.
Resolving deltas: 100% (83/83), done.
Checking connectivity... done.
Tapped 1 formula (3257 files, 24M)
```

#### brew install brew-cask

```Shell
faner@MBP-FAN:~|⇒  brew install brew-cask
==> Installing brew-cask from phinze/homebrew-cask
==> Cloning https://github.com/caskroom/homebrew-cask.git
Cloning into '/Library/Caches/Homebrew/brew-cask--git'...
remote: Counting objects: 3248, done.
remote: Compressing objects: 100% (3172/3172), done.
remote: Total 3248 (delta 82), reused 771 (delta 59), pack-reused 0
Receiving objects: 100% (3248/3248), 5.82 MiB | 467.00 KiB/s, done.
Resolving deltas: 100% (82/82), done.
Checking connectivity... done.
Note: checking out 'd39c95942f4226fb6c0e1a56c11008695ddeeade'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

==> Checking out tag v0.59.0
🍺  /usr/local/Cellar/brew-cask/0.59.0: 2976 files, 12M, built in 32 seconds
```

#### brew cask
执行 `brew cask` 或 `brew-cask` 可查看帮助概要（首次需输入 sudo 密码）：

```Shell
faner@MBP-FAN:~|⇒  brew cask
==> We need to make Caskroom for the first time at /opt/homebrew-cask/Caskroom
==> We'll set permissions properly so we won't need sudo in the future
Password:
brew-cask provides a friendly homebrew-style CLI workflow for the
administration of Mac applications distributed as binaries.

!! 
!! no command verb: 
!! 

Commands:

    audit      verifies installability of Casks
    cat        dump raw source of the given Cask to the standard output
    cleanup    cleans up cached downloads and tracker symlinks
    create     creates the given Cask and opens it in an editor
    doctor     checks for configuration issues
    edit       edits the given Cask
    fetch      downloads Cask resources to local cache
    home       opens the homepage of the given Cask
    info       displays information about the given Cask
    install    installs the given Cask
    list       with no args, lists installed Casks; given installed Casks, lists staged files
    search     searches all known Casks
    uninstall  uninstalls the given Cask
    update     a synonym for 'brew update'
    zap        zaps all files associated with the given Cask

See also "man brew-cask"
```

### command usage
**brew cask 大部分命令和 brew 保持一致，新增了以下几条命令：**

命令				 | 说明
------------------|----------------------------
brew cask audit   | 查询指定 Cask 的可安装性
brew cask cat     | 查看指定 Cask 安装源信息
brew cask zap     | 打包指定 Cask
brew cask cleanup | 清理缓存及软链

安装了 cask 之后，就可以像 brew 一样来搜索安装软件，以下示例 google-chrome ：

#### 搜索安装包

```Shell
faner@MBP-FAN:~|⇒  brew-cask search google-chrome
==> Exact match
google-chrome
```

#### 查询安装性

```Shell
faner@MBP-FAN:~|⇒  brew-cask audit google-chrome
audit for google-chrome: passed
```

#### 查询包信息
1.执行 `brew-cask cat ` 查询指定 Cask 的源信息：

```Shell
faner@MBP-FAN:~|⇒  brew-cask cat google-chrome
cask :v1 => 'google-chrome' do
  version :latest
  sha256 :no_check

  url 'https://dl.google.com/chrome/mac/stable/GGRO/googlechrome.dmg'
  name 'Google Chrome'
  homepage 'https://www.google.com/chrome/'
  license :gratis
  tags :vendor => 'Google'

  app 'Google Chrome.app'

  zap :delete => [
                  '~/Library/Application Support/Google/Chrome',
                  '~/Library/Caches/Google/Chrome',
                  '~/Library/Caches/com.google.Chrome',
                  '~/Library/Caches/com.google.Chrome.helper.EH',
                  '~/Library/Caches/com.google.Keystone.Agent',
                  '~/Library/Caches/com.google.SoftwareUpdate',
                  '~/Library/Google/GoogleSoftwareUpdate',
                  '~/Library/Logs/GoogleSoftwareUpdateAgent.log',
                 ],
      :rmdir  => [
                  '~/Library/Caches/Google',
                  '~/Library/Google',
                 ]

  caveats <<-EOS.undent
    The Mac App Store version of 1Password won't work with a Homebrew-Cask-linked Google Chrome. To bypass this limitation, you need to either:

      + Move Google Chrome to your /Applications directory (the app itself, not a symlink).
      + Install 1Password from outside the Mac App Store (licenses should transfer automatically, but you should contact AgileBits about it).
  EOS
end
```

2.执行 `brew-cask info ` 查询指定 Cask 的包信息：

```Shell
faner@MBP-FAN:~|⇒  brew-cask info google-chrome
google-chrome: latest
Google Chrome
https://www.google.com/chrome/
Not installed
https://github.com/phinze/homebrew-cask/blob/master/Casks/google-chrome.rb
==> Contents
  Google Chrome.app (app)
==> Caveats
The Mac App Store version of 1Password won't work with a Homebrew-Cask-linked Google Chrome. To bypass this limitation, you need to either:

  + Move Google Chrome to your /Applications directory (the app itself, not a symlink).
  + Install 1Password from outside the Mac App Store (licenses should transfer automatically, but you should contact AgileBits about it).

```

#### 安装/卸载 应用
`brew-cask` 安装和卸载的命令同 `brew` ，都是使用 `install / uninstall` 。

1.执行 `brew cask install google-chrome` 命令安装 google-chrome：

```Shell
brew cask install google-chrome
faner@MBP-FAN:~|⇒  brew cask install google-chrome   
==> Caveats
The Mac App Store version of 1Password won't work with a Homebrew-Cask-linked Google Chrome. To bypass this limitation, you need to either:

  + Move Google Chrome to your /Applications directory (the app itself, not a symlink).
  + Install 1Password from outside the Mac App Store (licenses should transfer automatically, but you should contact AgileBits about it).

==> Downloading https://dl.google.com/chrome/mac/stable/GGRO/googlechrome.dmg
######################################################################## 100.0%
==> Symlinking App 'Google Chrome.app' to '/Users/faner/Applications/Google Chrome.app'
🍺  google-chrome staged at '/opt/homebrew-cask/Caskroom/google-chrome/latest' (216 files, 183M)

```

2.执行 `brew cask uninstall google-chrome` 命令卸载 google-chrome：

```Shell
faner@MBP-FAN:~|⇒  brew-cask uninstall google-chrome
==> Removing App symlink: '/Users/faner/Applications/Google Chrome.app'
```

`uninstall` 命令将删除 `/opt/homebrew-cask/Caskroom/google-chrome` 及其在 `~/Applications/` 下的软链（symlink）。

#### [升级更新软件](https://github.com/phinze/homebrew-cask/issues/309)
brew-cask 并没有提供 brew 的 `upgrade` 命令，升级更新软件只能通过 DIY：

1. 进入应用软件的【关于】，手动检查是否可更新升级，使用软件自己的更新流程。
2. 卸载重装 (`brew cask uninstall ` && `brew cask install `) ，实现间接升级。

## 参考
[Mac 利器：brew、brew cask、zsh](http://my.oschina.net/evilgod528/blog/306548)  
[Mac安装软件新方法：Homebrew-cask](http://www.yangzhiping.com/tech/homebrew-cask.html)  
[简洁优雅的Mac OS X软件安装体验 - homebrew-cask](http://ksmx.me/homebrew-cask-cli-workflow-to-install-mac-applications/)  
[使用brew cask来安装Mac应用](http://blog.devtang.com/blog/2014/02/26/the-introduction-of-homebrew-and-brewcask/)  

[Launchrocket 帮助管理 Homebrew 安装的服务](http://www.osxtoy.com/?p=2431)  
[homebrew cask 安装 launchrocket](http://my.oschina.net/gujianhan/blog/204122)
