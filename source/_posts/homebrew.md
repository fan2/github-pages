title: Mac 下的软件安装 —— 从 pkg,dmg 到 brew,cask
date: 2015-11-22 15:05:00
categories: Mac
tags:
- DPKG
- APT
- RPM
- SRPM
- YUM
- pkg
- dmg
- MacPorts
- Homebrew
- brew
- brew-cask
---

本文前驱铺垫梳理了从 linux 源码安装软件到 DPKG/RPM 软件包管理机制，阐述了 *UNX 平台的软件安装包管理机制的演变和现代化。  
后半部分介绍了 Mac 下的软件安装包格式 —— pkg & dmg，以及软件包管理工具 —— brew 及 brew-cask，并梳理日常 Command Usage。

<!--more-->

## [linux 软件的安装与管理](http://blog.chinaunix.net/uid-509190-id-3056219.html)
[linux](https://github.com/torvalds/linux) 是一套免费使用和自由传播的基于 POSIX 和 UNIX 的多用户、多任务、支持多线程和多 CPU 的开源的类 Unix 操作系统。  
由于 linux 操作系统开放源代码，因而在其上安装的大部分也都是开源软件。开源软件的开发者往往仅需在开源代码托管平台上发布一份源码包，用户即可自由下载源码包到本地，基于源码包编译安装软件。这倒是非常符合 C 语言的设计哲学：一次编写，到处编译。

### [基于源码安装软件](http://www.cnblogs.com/huangfenghit/archive/2011/02/17/1957057.html)
基于源码安装软件一般由以下几个步骤组成：

- 下载解压源码
- 阅读 README、INSTALL 等说明文档，这一步至关重要
- 分析安装平台环境（ifconfigure）
- 编译安装软件（make，make install）

基于源代码安装软件的好处是：用户可以自由配置编译选项，按需编译实现功能定制，极大地满足**[个性化需求](http://www.zhihu.com/question/21949923)**。此外，用户还可以自己选择安装路径，方便管理。卸载软件也很方便，只需删除对应的安装目录即可。  
但是，配置、编译命令需要了解操作系统本身，并且依赖开源项目所使用的编程语言对应的工具链。为了使用一个应用软件，用户需要熟悉 linux 系统的文件组织架构和一堆  Shell 交互命令，还得解决编译过程中可能涉及到的繁杂的依赖关系。一定的英文水平也是必需的，关键是要有折腾不息的精神和顽强的动手能力！  
English、OS、Linux、命令行交互方式、...，这些令人望而却步的门槛足以将普通用户拒之门外。安装后，你可能都不知道[安装到哪里去了](http://www.linuxidc.com/Linux/2009-11/22842.htm)；甚至离开了 Windows 桌面，你都不知道从哪里启动软件。我依稀地记得刚接触 linux 那会儿，为了能在 linux 上播放个视频文件，废了老大劲才安装好一个 [MPlayer](http://blog.pfan.cn/xman/41044.html)。当时就森森地觉得 linux 真不是一般人能折腾得起，尽管现在已经折腾惯了，但依然觉得费脑伤神。  
作为一个曾经的[计算机旁系学生](http://www.tinylab.org/why-computer-students-learn-linux-open-source-technologies/)（自动化，别名计算机控制）、现在的半职业化软件攻城狮（涉猎甚广，浅显浮泛）和伪自由软件追崇者（用过盗版，不懂破解，从无贡献），因为工作关系经常穿梭于 Windows 和 Mac OS X 之间（游离其间，无所专精），不折腾下 Linux/Unix 都觉得没法混 github。  
尽管对于 vim 键盘型用户（估计基本都是程序员吧），很喜欢那种游离于指尖的"自由感"，但对于普通鼠标型 Windows 用户，[若无力驾驭，自由便是负担。](http://www.zhihu.com/question/20117703)  

> 只有开发人员才需要学习一个操作系统本身，用户只需要学习操作系统里面的每个“应用程序”怎么使用。 
> —— Linus Torvalds

### DPKG / RPM
伴随着 linux 的发展普及，linux 开发商开始在固定的硬件平台与操作系统上将要安装或升级的软件**编译好**，然后将这个软件的所有相关文件**打包**成一个特殊格式的文件。在这个软件内，还包含了预先检测系统与依赖软件（或动态链接库）的脚本，并提供记载该软件提供的所有文件信息等，最终将这个软件发布。  
客户端取得软件后，只要通过特定的命令来安装，那么该软件就会按照内部的脚本来检测相关的前驱软件是否存在。若安装的环境和条件符合要求，则开始安装。软件在安装完成后，还会将信息写入软件管理机制中，以便完成未来的升级、删除（反安装）等操作。

目前，在 Linux 界最常见的软件安装方式有两种：

1. DPKG

	- **DPKG**（Debian Packager）是由 [Debian Linux](https://www.debian.org/) 社区所开发出来的，著名的 `package` 概念由此被引入到 GNU/Linux 系统中。
	- 通过 DPKG 机制，Debian 提供的软件就能够简单安装起来，同时还能提供安装后的软件信息。派生于 Debian 的其他 [Linux](https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88%E5%88%97%E8%A1%A8) [Distributions](http://distrowatch.com/) 大多使用 dpkg 机制来管理软件，包括 [B2D](http://distrowatch.com/table.php?distribution=b2d)、[Ubuntu](http://www.ubuntu.com/global)、[Linux Mint](http://linuxmint.com/) 等。
	- Debian 为解决软件包更新问题，引入了 **APT**（Advanced Package Tool）[在线升级机制](http://www.cnblogs.com/kulin/archive/2012/07/31/2616490.html)，并在 `/etc/apt/sources.list` 文件列出了可获得软件包的镜像站点地址。
		- APT 由几个名字以 `apt-` 打头的程序组成，apt-get、apt-cache 和 apt-cdrom 是处理软件包的命令行工具。
		- apt 作为 dpkg 的前端工具，自动管理关联文件和维护已有配置文件，拥有出色的解决软件依赖问题的能力。

2. RPM

	-  **RPM**（RedHat Package Manager）是由 Red Hat 公司所开发出来的软件包管理程序。除了可以用来安装（-i）外，还可以进行查询（-q）、验证（-V）、更新（-U）、删除（-e）等操作，这些功能选项让软件的管理更加方便。包括 [Fedora](https://getfedora.org/)、[CenterOS](https://www.centos.org/)、[SUSE](https://www.suse.com/)/[openSUSE](https://www.opensuse.org/) 等知名的 linux 发行版本都使用 RPM 作为软件安装的管理机制。
	- RPM 包的封装格式一般有两种，分别是 **RPM** 和 **SRPM**。RPM 格式的文件 `xxx.rpm` 内含已经经过编译的二进制包和配置文件等数据。SRPM（Source RPM）对应的 RPM 文件类似于 `xxx.src.rpm` 格式，它包含了源码文件和一些编译指定的参数文件。因而，在使用的时候，需要先以 RPM 管理的方式编译为 RPM 文件([rpmbuild](http://www.centoscn.com/CentOS/2014/1029/4015.html)，再将编译完成的 RPM 文件安装到 Linux 系统中。
	- RPM 无法自动解决软件的依赖关系，使用与 APT 对应的 **[YUM](http://www.cnblogs.com/mchina/archive/2013/01/04/2842275.html)** ([Yellowdog](http://www.fixstars.com/en/technologies/linux/) Updater Modified) 机制可以**解决属性依赖问题**。**[YUM](http://www.cnblogs.com/chuncn/archive/2010/10/17/1853915.html)** 作为基于 RPM 的 Shell 前端软件包管理器，主要用于自动升级、安装/移除 RPM 软件包。它能够自动查找并解决 RPM 包之间的依赖关系，而无需管理员逐个手工的去安装每一个 RPM 包。

Distribution 代表 | 包管理机制 | 使用命令        | 在线升级机制(命令)
-----------------|-----------|---------------|-------------------
Red Hat / Fedora | RPM       | rpm，rpmbuild | YUM(yum)
Debian / Ubuntu  | DPKG      | dpkg          | APT(apt-get)

在 [linux 众多发行版](http://mitblog.pixnet.net/blog/post/41037058-10-%E5%A5%97-linux-%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1%E7%9A%84%E6%AF%94%E8%BC%83%E3%80%81ubuntu-vs-fedora-vs-cen)中，ubuntu 占领桌面，RHEL/[CentOS](http://www.g-loaded.eu/2009/10/05/fedora-server-vs-centos/) 占领服务器，比较小众的 **[Gentoo](https://www.gentoo.org/)** 采用独特的 **[Portage](https://zh.wikipedia.org/wiki/Portage)** 包管理系统。Gentoo 的软件树称为 Portage，对应的包管理器是 emerge，包元文件称为 ebuild。  
Gentoo 是个强调能自由选择的分发版，它使用源码来做包管理的方式。由于能自己编译及调整源码依赖等选项，而获得至高的自定义性及优化的软件，在源码包也有相当多新旧版本的选择，因此吸引了许多狂热爱好者以及专业人士。

#### RPM / SRPM
RPM 软件包命名规范：`name-version-release.arch.rpm`。  
其中，`version` 表示系统的发行版，如 fc18、el6 表明这个软件包是在 Fedora 18、RHEL 6.x / CentOS 6.x 下使用的；`arch` 表示硬件平台，常见的有 i386、x86_64 等。

**`rp-pppoe-3.1-5.i386.rpm`** 是一个典型的 RPM 安装包，它由几个部分构成：

- `rp-pppoe`：名称
- `3.1`：版本信息
- `5`：发布次数
- `.i386`：适合的硬件平台
- `.rpm`：扩展名

RPM 文件必须要在相同的 linux 环境才能安装，而 SRPM 是源代码格式。我们可以通过修改 SRAM 内的参数，按需编译生成适合我们 linux 环境的 RPM 文件，而不必与原作者打包的 Linux 环境相同。  
通常一个软件在发布的时候，都会同时释放出该软件的 RPM 与 SRPM。

文件格式 | 扩展名格式    | 直接安装与否 | 内含程序类型 | 可否修改参数并编译
--------|-------------|-----------|-------------|----------------
RPM     | `xxx.rpm`     | √         | 已编译       | ×
SRPM    | `xxx.src.rpm` | ×         | 未编译的源码  | √

RPM 建包的原理并不复杂，可以理解为按照标准的格式整理一些信息，包括：软件基础信息，以及安装、卸载前后执行的[脚本](http://hlee.iteye.com/blog/343499)，对源码包解压、打补丁、编译，安装路径和文件等。我们可以基于标准规范，来[使用 rpmbuild 制作自己的 RPM 包](http://hlee.iteye.com/blog/343499)。

## [Mac 软件包管理工具](https://github.com/pubyun/macdev/blob/master/basic.md)
linux 平台下的 apt-get 和 yum 命令行工具分别适用于 deb、rpm 包管理方式的发行版本，主要用于自动从互联网的软件仓库中搜索、安装、升级和卸载软件。在 Mac OS X 平台下，除了直接从 AppleStore 下载认证上架的软件进行安装外，还可以在系统偏好设置的【安全性与隐私】中允许从**任何来源**下载的应用。  

### dmg & pkg
一些应用会提供 [dmg、pkg](http://www.xitongzhijia.net/xtjc/20150303/39862.html) 安装包，例如 `git-2.5.3-intel-universal-mavericks.dmg`、`Subversion-1.9.2_10.10.x.pkg`。

1. **dmg** 是苹果的压缩镜像文件（类似 Windows 下的 iso ），它是 Mac 应用软件通用的打包格式（相当于 ipa），里面一般包含 `应用程序.app` 的图标和一个应用程序文件夹（`/Applications`）快捷方式，直接将 `应用程序.app` 拖曳至应用程序文件夹即可完成安装。卸载也同样绿色，直接在 `Launchpad` 中或 cd 到  `/Applications` 目录下删除应用（文件夹）即可。
2. **[pkg](https://en.wikipedia.org/wiki/.pkg)** 属于系统级软件的安装程序，相当于 iOS 越狱后装的 deb，一般会修改系统配置，权限较高。pkg 安装一般要求 sudo 授权，[卸载 pkg 安装的应用](http://blog.csdn.net/play_fun_tech/article/details/27964861) 也比较麻烦。pkg 类似 Windows 下的安装程序 Setup.exe 和 *.[msi](https://msdn.microsoft.com/en-us/library/cc185688(v=vs.85).aspx) 。  
	Windows 下可以使用 [Install Shield](http://www.flexerasoftware.com/producer/products/software-installation/installshield-software-installer/) 来 [制作安装程序](http://www.yesky.com/460/1843460.shtml)，可使用 Xcode 自带的 [PackageMaker](http://www.identityfinder.com/kb/Enterprise-Documentation/046141) 或打包命令行工具 [pkgbuild](http://developer.apple.com/library/mac/documentation/Darwin/Reference/Manpages/man1/pkgbuild.1.html)+[productbuild](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/productbuild.1.html)+[pkgutil](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/pkgutil.1.html) 或 [Iceberg](http://s.sudre.free.fr/Software/Iceberg.html)（an Integrated Packaging Environment (IPE) ）来[制作安装包](http://blog.csdn.net/handsomerocco/article/details/7761212)。
3. **mpkg**：pkg 是单个[应用程序的安装包](http://blog.csdn.net/dongdongdongjl/article/details/7896771)，而 mpkg（multi pkg）是多个 pkg 。我们来看一下从 AppleStore 下载的 OS X EI Capitan 安装器文件——`安装 OS X EI Capitan.app` 的 `/Contents/SharedSupport` 目录：

	```Shell
	faner@MBP-FAN:/Applications/Install OS X El Capitan.app/Contents/SharedSupport|
	⇒  tree 
	.
	├── InstallESD.dmg
	└── OSInstall.mpkg
	
	0 directories, 2 files
	```

	> 下载完 `安装 OS X EI Capitan.app` 之后，可以使用 **`createinstallmedia`** 命令[制作 U 盘安装盘](http://bbs.feng.com/read-htm-tid-9930245.html)，或[恢复到 U 盘制作启动盘](http://bbs.feng.com/read-htm-tid-5045869.html)。

4. **dmg with pkg**：像 `git-2.5.3-intel-universal-mavericks.dmg` 这种 dmg 打包的是 git command CLI 的安装 pkg，需要使用 DiskImageMounter 挂载 dmg，然后打开 pkg（使用 Installer），按照引导一步步 next 即可安装完成。当然也可使用 [命令行](http://www.it165.net/os/html/201207/2764.html) 完成挂载安装操作。

除了使用 dmg、pkg 来安装软件外，Mac 下同样有优秀的软件包管理工具，可以下载、安装和管理大量 AppleStore 没有提供、而又经常会用到的开源软件。我们有两种选择 —— MacPorts 和 Homebrew。

### [MacPorts](http://www.macports.org/)
[MacPorts](http://chenpeng.info/html/1753) (DarwinPorts) 是由 [FreeBSD](http://www.freebsd.org/) 的 port 移植而来的软件包管理系统，，用来简化 Mac OS X和 Darwin 操作系统上软件的安装。在 Mac 中安装 MacPorts [让你在Mac 的 Shell 下更加游刃有余](http://www.linuxidc.com/Linux/2012-01/52111.htm)。  
[MacPorts](http://blog.csdn.net/maojudong/article/details/7918278) 有个原则，对于软件包之间的依赖，都在 MacPorts 内部（`/opt/local`）解决，无论系统本身是否包含了需要的库，都不会加以利用。这使得 MacPorts 庞大臃肿，导致系统出现大量软件包的冗余，占用不小的磁盘空间，同时稍大型一点的软件编译时间都会难以忍受。

### [Homebrew](http://brew.sh/)
[Homebrew](http://blog.csdn.net/delphiwcdj/article/details/19679891)（简称 brew）是 Mac OS X 不可或缺的软件管理工具(The missing package manager for OS X)，[让 Mac 拥有类似 apt-get 的功能](http://snowolf.iteye.com/blog/774312)，用以简化软件的安装、升级和卸载过程。

> **Homebrew** is the easiest and most flexible way to install the UNIX tools.

Homebrew 是一款使用 Ruby 进行开发的托管于 [GitHub](https://github.com/Homebrew/homebrew) 上的自由及开放源代码的软件包管理系统，通过用户的贡献扩大对软件包的支持。

### MacPorts vs Homebrew
MacPorts 和 Homebrew 这两种包管理系统的工作方式都是下载源代码，然后在本地编译。但是这两种包管理系统还是存在很大差异的，主要差异有以下三点：

- MacPorts 的理念是尽量减少对系统现有库的依赖，编译时间较长；而 Homebrew 则是尽量依赖系统现有库，编译时间会显著减少。
- MacPorts 的 Package 是安装到 `/opt/local` ，不会与系统现有的软件发生冲突；而 Homebrew 的 Package 是安装到 `/usr/local`，可能与系统自带的软件发生冲突。
- Macports 使用 rsync 进行同步（[也可以使用svn](https://trac.macports.org/wiki/howto/SyncingWithSVN)），而 Homebrew 使用 git 进行同步。

总体来说，Homebrew 使用简单，编译时间短，比较适合新手使用。MacPorts 编译时间长，命令还要带上 sudo，易用性上没有 Homebrew 好，但是比较干净，适合有洁癖的人使用。

以上梳理了 Linux 和 Mac 下的包管理机制，下面重点介绍 Mac OS X 上的包管理 CLI 命令行工具 brew 及其增强工具 brew-cask。

## brew (homebrew)
**brew** 是从软件包仓库下载源代码码到本地进行解压，进而执行 `./configure` && `make install` ，将软件编译安装到单独的目录（`/usr/local/Cellar`）下，然后软链（symlink）到 `/usr/local` 目录下，同时会自动检测下载相关依赖库，并自动配置好各种环境变量。  
这个对程序员来说简直是福音，使用简单的指令就能快速安装、升级和卸载本地的各种开发环境。

## 安装 brew
brew 的安装很简单，使用一条 ruby 命令调用 curl 下载安装即可。

**1.Mac OS X 上默认已安装 [ruby](https://www.ruby-lang.org/)：**

```
faner@MBP-FAN:~|⇒  ruby -v
ruby 2.0.0p645 (2015-04-13 revision 50299) [universal.x86_64-darwin15]
```

**2.Mac OS X 上默认已内置了 [curl](http://curl.haxx.se/)（Command Line URL Viewer）：**

```
faner@MBP-FAN:~|⇒  curl --version
curl 7.43.0 (x86_64-apple-darwin15.0) libcurl/7.43.0 SecureTransport zlib/1.2.5
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp smb smbs smtp smtps telnet tftp 
Features: AsynchDNS IPv6 Largefile GSS-API Kerberos SPNEGO NTLM NTLM_WB SSL libz UnixSockets
```

**说明：**

> **curl** 是基于跨平台网络协议库 [libcurl](http://blog.csdn.net/mac_cm/article/details/6670154) 的利用 URL 语法在命令行方式下工作的开源文件传输工具。
> [curl常用命令](http://www.cnblogs.com/gbyukg/p/3326825.html) 可参考  [curl 详解](http://blog.csdn.net/fudesign2008/article/details/7608619)、[curl网站开发指南](http://www.ruanyifeng.com/blog/2011/09/curl.html)。

### [旧的安装](http://blog.csdn.net/jiajiayouba/article/details/44261011)
很早之前，homebrew 的安装 url 是 `https://raw.github.com/Homebrew/homebrew/go/install`：

```Shell
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)" 
```

首次执行 `brew install` 安装软件时，将提示不能创建 `/usr/local/Cellar` 目录，原因是没有 `/usr/local` 的写权限。

```Shell
➜  ~  brew install node
Error: Could not create /usr/local/Cellar
Check you have permission to write to /usr/local
```

**[解决方法](http://segmentfault.com/q/1010000000505091)：**

> 执行 `chown` 命令改变 `/usr/local` 的所有者为 `whoami` ，`-R` 表示递归（Recursive）。

```Shell
➜  ~  whoami
faner

➜  ~  sudo chown -R faner /usr/local
```

### 新的安装
可以在 [brew 的官网首页](http://brew.sh/index.html)的末尾看到最新的安装代码：

```Shell
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

最新的安装过程中将会要求输入密码授权 `sudo chown ${whoami} /usr/local/*` ：

```Shell
~ $ brew
-bash: brew: command not found
~ $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
==> This script will install:
/usr/local/bin/brew
/usr/local/Library/...
/usr/local/share/man/man1/brew.1
==> The following directories will be made group writable:
/usr/local/.
/usr/local/bin
/usr/local/include
/usr/local/lib
/usr/local/lib/pkgconfig
==> The following directories will have their owner set to thomasfan:
/usr/local/.
/usr/local/bin
/usr/local/include
/usr/local/lib
/usr/local/lib/pkgconfig
==> The following directories will have their group set to admin:
/usr/local/.
/usr/local/bin
/usr/local/include
/usr/local/lib
/usr/local/lib/pkgconfig

Press RETURN to continue or any other key to abort
==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/. /usr/local/bin /usr/local/include /usr/local/lib /usr/local/lib/pkgconfig
Password:
==> /usr/bin/sudo /usr/sbin/chown thomasfan /usr/local/. /usr/local/bin /usr/local/include /usr/local/lib /usr/local/lib/pkgconfig
==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/. /usr/local/bin /usr/local/include /usr/local/lib /usr/local/lib/pkgconfig
==> /usr/bin/sudo /bin/mkdir /Library/Caches/Homebrew
==> /usr/bin/sudo /bin/chmod g+rwx /Library/Caches/Homebrew
==> /usr/bin/sudo /usr/sbin/chown thomasfan /Library/Caches/Homebrew
==> Downloading and installing Homebrew...
remote: Counting objects: 3847, done.
remote: Compressing objects: 100% (3691/3691), done.
remote: Total 3847 (delta 40), reused 520 (delta 21), pack-reused 0
Receiving objects: 100% (3847/3847), 3.31 MiB | 105.00 KiB/s, done.
Resolving deltas: 100% (40/40), done.
From https://github.com/Homebrew/homebrew
 * [new branch]      master     -> origin/master
HEAD is now at e191c00 jsonnet: update 0.8.5 bottle.
==> Installation successful!
==> Next steps
Run `brew help` to get started
~ $ 
```

## brew -v / --version
安装完 brew，执行 `brew -v` 命令可以查看安装的 brew 版本信息：

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

### brew 常用命令

命令				      | 说明
-----------------------|---------------------
brew update            | 更新 brew
brew search FORMULA    | 查找软件包，可使用正则表达式
brew info FORMULA      | 显示软件的信息
brew deps FORMULA      | 显示包依赖
brew install FORMULA   | [安装软件包](http://www.cnblogs.com/TankXiao/p/3247113.html)
brew uninstall FORMULA | 卸载软件包
brew list              | 列出已安装的软件包，可指定 FORMULA
brew outdated          | 列出可升级的软件包
brew upgrade           | 更新已安装的软件包，可指定 FORMULA
brew doctor            | 诊断 homebrew 环境
brew prune             | 删除 /usr/local 下的无效链接(remove broken symlinks)

### brew info
**`brew info`** 显示软件包信息，一般包括：

- 软件概要信息
- 本地是否已安装：Not installed / Poured from bottle
- 依赖包：==> Dependencies，Build、Required、Recommended、Optional
- 编译、安装选项： ==> Options
- 预警信息：==> Caveats

[brew info 查看 plist 文件](https://ruby-china.org/topics/21050)

### brew install axel
以下示例查找比 wget 下载速度高几倍的支持[断点续传](http://www.pooy.net/axel-download-helper.html)的[多线程](http://www.cnblogs.com/SunWentao/archive/2008/07/10/1239924.html)下载 CLI 命令行工具 **[axel](http://wilmer.gaast.net/main.php/axel.html)**，并显示软件包信息和依赖关系，然后安装该工具：

```Shell
faner@MBP-FAN:~|⇒  brew search axel
axel
faner@MBP-FAN:~|⇒  brew info axel
axel: stable 2.4 (bottled)
Light UNIX download accelerator
https://packages.debian.org/sid/axel
Not installed
From: https://github.com/Homebrew/homebrew/blob/master/Library/Formula/axel.rb
faner@MBP-FAN:~|⇒  brew deps axel
faner@MBP-FAN:~|⇒  
faner@MBP-FAN:~|⇒  brew install axel
==> Downloading https://homebrew.bintray.com/bottles/axel-2.4.el_capitan.bottle.1.tar.gz
###############################################                           66.5%
curl: (56) SSLRead() return error -9806
Error: Failed to download resource "axel"
Download failed: https://homebrew.bintray.com/bottles/axel-2.4.el_capitan.bottle.1.tar.gz
Warning: Bottle installation failed: building from source.
==> Downloading https://mirrors.ocf.berkeley.edu/debian/pool/main/a/axel/axel_2.4.orig.tar.gz
######################################################################## 100.0%
==> ./configure --prefix=/usr/local/Cellar/axel/2.4 --debug=0 --i18n=0
==> make
==> make install
🍺  /usr/local/Cellar/axel/2.4: 8 files, 104K, built in 13 seconds
faner@MBP-FAN:~|⇒  axel -V
Axel version 2.4 (Darwin)

Copyright 2001-2002 Wilmer van der Gaast.
```

1. 从 homebrew 官方仓库 `homebrew.bintray.com` 下载 axel 失败后，自动从镜像源 `mirrors.ocf.berkeley.edu` 重新下载。
2. 下载完成后，依次执行 `./configure`、`make` 和 `make install` 将软件编译安装到 `/usr/local/Cellar/axel/` 目录下，根据版本存放到文件夹 `2.4` 下。

**注：**

> homebrew 下载源码的缓存目录为 `/Library/Caches/Homebrew/`。

## [brew cask](http://www.zhihu.com/question/22624898)
Homebrew 作为 Ruby 社区极富想象力的作品，使得 Mac 下安装 Mysql 等常用包不再困难。那么，是否也可以通过 `brew install mysql` 这样简单的方式来安装 Google Chrome 浏览器呢？为解决这一问题，phinze 的作品 [homebrew-cask](https://github.com/phinze/homebrew-cask) 应运而生。

### about
Homebrew 可以管理 Mac 下的命令行工具（wget、node），**brew cask** 则是一套建立在 brew 上的**增强**命令行工具，支持管理 Mac 下的 GUI 程序，例如 qq、 google-chrome、evernote 等。  
cask 从镜像源下载已经编译好了的[应用软件二进制包](https://github.com/phinze/homebrew-cask/tree/master/Casks)（.dmg/.pkg）到本地解压到单独的目录（`/opt/homebrew-cask/Caskroom`）下，然后软链（symlink）到 `/Applications` 目录下。  
cask 包含了很多在 AppStore 里没有的常用软件，省掉了手动下载、解压、拖拽（安装）等步骤，且卸载也相当容易与干净，使用起来非常方便。

假设你已安装好了 Homebrew，执行以下命令查找 `cask` 包并显示软件包信息和依赖关系：

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

### install
安装 cask 极其简单，打开终端输入：

```Shell
brew tap phinze/homebrew-cask
brew install brew-cask
```

#### brew tap phinze/homebrew-cask

由于 brew 和包含的包源都是通过 github 来管理，人为的维护管理。除了默认的仓库，还允许别人的源添加进来。通过 [`brew tap`](http://icyleaf.com/2014/01/homebrew-hidden-commands/) 指定第三方包源：

> $ brew tap <gihhub_user/repo>

默认的源是 `caskroom/cask`，以下显式 tap 原作者的源（`phinze/homebrew-cask`）：

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

本机已经安装了 `brew-cask` 的依赖软件 **`emacs`** :

```Shell
faner@MBP-FAN:~|⇒  whereis emacs
/usr/bin/emacs

faner@MBP-FAN:~|⇒  emacs --version
GNU Emacs 22.1.1
Copyright (C) 2007 Free Software Foundation, Inc.
GNU Emacs comes with ABSOLUTELY NO WARRANTY.
You may redistribute copies of Emacs
under the terms of the GNU General Public License.
For more information about these matters, see the file named COPYING.
```

如果没有安装 **`emacs`**，则 brew 在安装 cask 之前会自动帮我们下载安装 `emacs`：

```Shell
==> Installing dependencies for cask: emacs
==> Installing cask dependency: emacs
==> Downloading https://homebrew.bintray.com/bottles/emacs-24.5.yosemite.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring emacs-24.5.yosemite.bottle.1.tar.gz
==> Caveats
To have launchd start emacs at login:
  ln -sfv /usr/local/opt/emacs/*.plist ~/Library/LaunchAgents
Then to load emacs now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.emacs.plist
==> Summary
🍺  /usr/local/Cellar/emacs/24.5: 3915 files, 105M
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

执行 `brew-cask --version` 命令可以查看安装的 brew-cask 版本信息：

```Shell
faner@MBP-FAN:~|⇒  brew-cask --version
0.59.0
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
我们也可手动删除 homebrew 下载目录中缓存的 google-chrome 安装包  `/Library/Caches/Homebrew/google-chrome-latest.dmg` 及其软链 `/Library/Caches/Homebrew/Casks/google-chrome-latest.dmg` 。

#### [升级更新软件](https://github.com/phinze/homebrew-cask/issues/309)
brew-cask 并没有提供 brew 的 `upgrade` 命令，升级更新软件只能通过 DIY：

1. 进入应用软件的【关于】，手动检查是否可更新升级，使用软件自己的更新流程。
2. 卸载重装 (`brew cask uninstall ` && `brew cask install `) ，实现间接升级。

### [安装文件预览插件](http://www.cocoachina.com/mac/20141112/10198.html)
有些插件可以让 Mac 上的文件预览更有效，比如语法高亮、markdown 渲染、json 预览等等。通过 `brew cask install` 命令可以安装这些插件：

```Shell
$ brew cask install qlcolorcode
$ brew cask install qlstephen
$ brew cask install qlmarkdown
$ brew cask install quicklook-json
$ brew cask install qlprettypatch
$ brew cask install quicklook-csv
$ brew cask install betterzipql
$ brew cask install webp-quicklook
$ brew cask install suspicious-package
```

### 安装 Visual Studio Code

```Shell
~ $ brew cask search visual-studio-code
==> Exact match
visual-studio-code
~ $ brew-cask cat visual-studio-code
cask :v1 => 'visual-studio-code' do
  version '0.10.1'
  sha256 'b71089670b3c2a259bf26ad6a6ad7b0abc9bb805353e8087f5c86361a5f8defc'

  # vo.msecnd.net is the official download host per the vendor homepage
  url "https://az764295.vo.msecnd.net/public/#{version}-release/VSCode-darwin.zip"
  name 'Visual Studio Code'
  homepage 'https://code.visualstudio.com/'
  license :mit
  tags :vendor => 'Microsoft'

  app 'Visual Studio Code.app'

  zap :delete => [
                  '~/Library/Application Support/Code',
                  '~/Library/Caches/Code',
                 ]
end
~ $ brew-cask info visual-studio-code
visual-studio-code: 0.10.1
Visual Studio Code
https://code.visualstudio.com/
Not installed
https://github.com/caskroom/homebrew-cask/blob/master/Casks/visual-studio-code.rb
==> Contents
  Visual Studio Code.app (app)
  ~ $ brew-cask audit visual-studio-code
audit for visual-studio-code: passed
~ $ brew cask install visual-studio-code
==> Downloading https://az764295.vo.msecnd.net/public/0.10.1-release/VSCode-darwin.zip
######################################################################## 100.0%
==> Symlinking App 'Visual Studio Code.app' to '/Users/thomasfan/Applications/Visual Studio Code.app'
🍺  visual-studio-code staged at '/opt/homebrew-cask/Caskroom/visual-studio-code/0.10.1' (1675 files, 177M)
```

## 参考
[Mac 利器：brew、brew cask、zsh](http://my.oschina.net/evilgod528/blog/306548)  
[Mac安装软件新方法：Homebrew-cask](http://www.yangzhiping.com/tech/homebrew-cask.html)  
[简洁优雅的Mac OS X软件安装体验 - homebrew-cask](http://ksmx.me/homebrew-cask-cli-workflow-to-install-mac-applications/)  
[使用brew cask来安装Mac应用](http://blog.devtang.com/blog/2014/02/26/the-introduction-of-homebrew-and-brewcask/)  
[Mac下通过 brew 安装不同版本的 PHP](http://blog.csdn.net/flymorn/article/details/43112813)  

[Launchrocket 帮助管理 Homebrew 安装的服务](http://www.osxtoy.com/?p=2431)  
[homebrew cask 安装 launchrocket](http://my.oschina.net/gujianhan/blog/204122)
