title: 基于 Hexo 和 GitHub Pages 搭建博客
date: 2015-11-12 19:27:00
categories: Hexo
tags:
- node.js
- npm
- hexo
- next
- feed
- analytics
- sitemap
---

本文详细记录了从安装 node.js / Hexo 到 使用 hexo 发布 markdown 格式的博客并托管到 GitHub 的全过程。

<!--more-->

## 配置环境
本文的操作流程和步骤基于 MacBook Pro + OS X EI Capitan 平台。

## 安装 Node.js + NPM
### 什么是 Node.js ?
[Node.js][nodejs-cn] 是一个基于 [Chrome V8][] 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。Node.js 的包管理器 [npm][]，是全球最大的跨平台开源库生态系统。  
正如 [维基百科][] 所说：“Node.js 是谷歌 V8 引擎、libuv 平台抽象层以及主体使用 Javscript 编写的核心库三者集合的一个包装外壳。”   

> 注：V8是谷歌开发的，目前公认最快的 Javascript 解析引擎；libuv 是一个开源的、为 Node 定制而生的跨平台异步 IO 库。

Node.js 服务器端和网络应用程序是使用 JavaScript 脚本语言编写，并可以在 Mac OS X、Microsoft Windows 和 Linux 内的 Node.js 运行时运行。  
Node.js还提供了丰富的各种模块的 [JavaScript][Node.js API] 库，从而简化了 Node.js 的使用，方便 Web 应用程序的研究与开发。

> Node.js = Runtime Environment + JavaScript Library

[Node.js 究竟是什么？][] [为什么要用 Node.js ?][]  
[使用 Node.js 的优势和劣势都有哪些？][] [谁在使用 Node.js ?][]  

### 安装 Node.js
可以到 [Node.js 官网][nodejs-en] 上下载最新的 node-v5.0.0.pkg for OS X (x64) 安装包到本地进行安装。  
也可以使用 brew（The missing package manager for OS X）包管理器命令行工具进行安装。  
#### Homebrew
brew 全称为 [Homebrew][]，是 UNIX 上的软件包管理工具。  

> Homebrew  is  the easiest and most flexible way to install the UNIX tools.

在终端使用 `brew -v` 或 `brew --version` 命令来查看 Mac 上安装的 Homebrew 版本：

```Shell
➜  ~  brew -v  
Homebrew 0.9.5 (git revision 9cbe8; last commit 2015-11-08)
```

如果没有安装 brew，可在 [Mac 上安装 Homebrew][] [使 OS X 更完整][]。

#### brew install node
执行 `brew install node` 命令使用 brew 安装 node：

```
➜  ~  brew install node
==> Downloading https://homebrew.bintray.com/bottles/node-4.2.1.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring node-4.2.1.el_capitan.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/node/4.2.1: 2738 files, 36M
```

node installed completion 之后，执行 `node -v` 命令可查看安装的 node.js 版本：

```Shell
➜  ~  node -v
v4.2.1
```

执行 `brew upgrade node` 命令可升级到最新的 node 5.1.0 ：

```
faner@MBP-FAN:~|⇒  brew upgrade node
==> Upgrading 1 outdated package, with result:
node 5.1.0
==> Upgrading node
==> Downloading https://homebrew.bintray.com/bottles/node-5.1.0.el_capitan.bottle.tar.gz

```

#### npm
Node.js 默认内置了模块管理工具 —— NPM（Node Package Manager），其灵感来源于 [RubyGems][]（具有版本和依赖管理功能，可以通过在线资料库便捷安装可重用的组件的管理工具）。  
NPM 生态系统向所有人开放，任何人都可以发布自己的模块，所有的模块都可以在 NPM 资料库中找到。一个完整的公用模块列表可以在 [NPM 的网站][] 上找到，也可以通过与 Node.js 一同安装的 NPM 客户端命令行工具访问。  
可执行 `npm -v` 命令查看 `node.js` 集成的 npm 包管理工具的版本：

```Shell
➜  ~  npm -v
2.14.7
```

执行 `npm help` 命令可查看帮助：

```Shell
➜  ~  npm help

Usage: npm <command>

where <command> is one of:



npm <cmd> -h     quick help on <cmd>
npm -l           display full usage info
npm faq          commonly asked questions
npm help <term>  search for help on <term>
npm help npm     involved overview

Specify configs in the ini-formatted file:
    /Users/faner/.npmrc
or on the command line via: npm <command> --key value
Config info can be viewed via: npm help config

npm@2.14.7 /usr/local/lib/node_modules/npm
```

执行 `npm -l` 命令可查看帮助详情。

下文将通过 `npm install <pkg>` 命令来安装快速、简洁且高效的[静态博客框架][] —— [Hexo][]，并基于 GitPages 托管来搭建免费开源博客。

## NPM 安装 hexo
### npm install hexo
执行 `npm install -g hexo` 命令安装 hexo：

```Shell
➜  ~  npm install -g hexo

> fsevents@0.3.8 install /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/chokidar/node_modules/fsevents
> node-gyp rebuild

> dtrace-provider@0.6.0 install /usr/local/lib/node_modules/hexo/node_modules/bunyan/node_modules/dtrace-provider
> node scripts/install.js


> fsevents@1.0.5 install /usr/local/lib/node_modules/hexo/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents
> node-pre-gyp install --fallback-to-build

[fsevents] Success: "/usr/local/lib/node_modules/hexo/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents/lib/binding/Release/node-v46-darwin-x64/fse.node" is installed via remote
/usr/local/bin/hexo -> /usr/local/lib/node_modules/hexo/bin/hexo
hexo@3.1.1 /usr/local/lib/node_modules/hexo

```

**说明：**

> npm默认为当前项目安装，`-g` 表示全局安装。

### hexo version
可执行 `hexo -v` 或  `hexo -version` 命令来检验安装的 hexo 版本：

```Shell
➜  ~  hexo -v
hexo-cli: 0.1.8
os: Darwin 15.0.0 darwin x64
http_parser: 2.5.0
node: 4.2.1
v8: 4.5.103.35
uv: 1.7.5
zlib: 1.2.8
ares: 1.10.1-DEV
icu: 56.1
modules: 46
openssl: 1.0.2d
```

### hexo help
可执行 `hexo --help` 查看帮助：

```Shell
➜  ~  hexo --help
Usage: hexo <command>

Commands:
  help     Get help on Hexo.
  init     Create a new Hexo folder.
  version  Display version information.

Global Options:
  --config  Specify config file instead of using _config.yml
  --cwd     Specify the CWD
  --debug   Display all verbose messages in the terminal
  --safe    Disable all plugins and scripts
  --silent  Hide output on console

For more help, you can use 'hexo help [command]' for the detailed information
or you can check the docs: http://hexo.io/docs/
```

**常用的 hexo 命令：**

- hexo init `<blog>` ：初始化 hexo 博客模板。
- hexo n == hexo new：新建文章或页面。
- hexo g == hexo generate：编译生成网站。
- hexo d == hexo deploy：发布网站。
- hexo s == hexo server：启动服务器。
- hexo clean：清理 cache。

**hexo 在线帮助文档：**

- <http://hexo.io/docs/>

## hexo init
假设我们接下来将在本地工程目录 `Projects/git/blog` 下构建博客站点（WebSite）。  
cd 进入 `Projects/git/blog` 目录，针对该目录执行 `hexo init` 命令。

```Shell
➜  ~  cd Projects/git
➜  git  mkdir blog
➜  git  cd blog
➜  blog  hexo init
INFO  Copying data to ~/Projects/git/blog
INFO  You are almost done! Don't forget to run 'npm install' before you start blogging with Hexo!
```

### 博客模板
`hexo init` 初始化成功后，可调用 `ls` 或 [`tree`][ls-tree-mac] 命令查看 hexo 在 blog 目录下生成的模板文件：

```Shell
➜  blog  ls
_config.yml  package.json scaffolds    source       themes
➜  blog  tree -L 2
.
├── _config.yml
├── package.json
├── scaffolds
│   ├── draft.md
│   ├── page.md
│   └── post.md
├── source
│   └── _posts
└── themes
    └── landscape

5 directories, 5 files
```

文件/目录     | 作用
-------------|-------------------------
_config.yml  | 网站的配置信息
package.json | 应用程序的信息，包括依赖包(dependencies)
scaffolds    | 模板文件夹，Hexo 会根据 scaffold 来新建文件。
source       | 资源文件夹，`_post` 目录存放博客源码（markdown文件）
themes       | 主题文件夹，默认主题为landscape

`source` 下除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件 将会被忽略。`/source/_posts` 目录下默认有一个欢迎页 —— `hello-world.md` 文件。  
在下文使用 `hexo generate` 生成网站时，Hexo 会编译 Markdown 和 HTML 源文件，根据主题来生成静态页面。静态页面文件将会被存放到 `public` 文件夹，而其他文件会被拷贝过去视作资源。

### npm install
此时，尝试执行 `hexo generate` 生成包含 Hello World 首页的博客，将提示需要执行
`npm install hexo --save` :

```Shell
➜  blog  hexo generate
ERROR Local hexo not found in ~/Projects/git/blog
ERROR Try running: 'npm install hexo --save'
```

实际上，我们已经执行过 `npm install hexo` 。根据上一步 `hexo init` 的提示，在正式 blogging with Hexo 之前，需要执行 `npm install` 命令安装 `package.json` 里的依赖包（Dependencies）：

```
➜  blog  hexo generate
ERROR Local hexo not found in ~/Projects/git/blog
ERROR Try running: 'npm install hexo --save'

➜  blog  npm install

➜  blog  ls
_config.yml  node_modules package.json scaffolds    source       themes

```

`ls` 可以查看到目录下将多出 `node_modules` 文件夹，其中存放 `npm install` 安装的依赖包。

## hexo generate
上面，我们在执行 `hexo init` 后，再执行 `npm install` 安装了 package.json 所依赖的包。此时，执行 `hexo generate` 命令将编译（只有欢迎页：`hello-world.md`）生成待发布的博客文件。

```Shell
➜  blog  hexo generate
INFO  Files loaded in 290 ms
INFO  Generated: js/script.js
INFO  Generated: fancybox/jquery.fancybox.pack.js
INFO  Generated: fancybox/jquery.fancybox.js
INFO  Generated: fancybox/jquery.fancybox.css
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.js
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.css
INFO  Generated: fancybox/helpers/jquery.fancybox-media.js
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.js
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.css
INFO  Generated: fancybox/helpers/fancybox_buttons.png
INFO  Generated: fancybox/fancybox_sprite@2x.png
INFO  Generated: fancybox/fancybox_sprite.png
INFO  Generated: fancybox/fancybox_overlay.png
INFO  Generated: fancybox/fancybox_loading@2x.gif
INFO  Generated: fancybox/fancybox_loading.gif
INFO  Generated: fancybox/blank.gif
INFO  Generated: css/style.css
INFO  Generated: css/images/banner.jpg
INFO  Generated: css/fonts/fontawesome-webfont.woff
INFO  Generated: css/fonts/fontawesome-webfont.ttf
INFO  Generated: css/fonts/fontawesome-webfont.svg
INFO  Generated: css/fonts/fontawesome-webfont.eot
INFO  Generated: css/fonts/FontAwesome.otf
INFO  Generated: 2015/11/08/hello-world/index.html
INFO  Generated: archives/index.html
INFO  Generated: archives/2015/index.html
INFO  Generated: archives/2015/11/index.html
INFO  Generated: index.html
INFO  28 files generated in 794 ms
➜  blog  ls
_config.yml  node_modules public       source
db.json      package.json scaffolds    themes
```

`ls` 查看目录下将生成 `public` 和 `db.json` 这两个文件夹。

1. `db.json` —— json格式的静态常量数据库。  
2. `public` 文件夹存放编译后的静态网站（html+css+js），也就是最后要发布的博客。  

### public
在 public 目录下执行 `tree -L 2`，可以查看其目录结构：

```Shell
➜  blog  cd public 
➜  public  tree -L 2
.
├── 2015
│   └── 11
├── archives
│   ├── 2015
│   └── index.html
├── css
│   ├── fonts
│   ├── images
│   └── style.css
├── fancybox
│   ├── blank.gif
│   ├── fancybox_loading.gif
│   ├── fancybox_loading@2x.gif
│   ├── fancybox_overlay.png
│   ├── fancybox_sprite.png
│   ├── fancybox_sprite@2x.png
│   ├── helpers
│   ├── jquery.fancybox.css
│   ├── jquery.fancybox.js
│   └── jquery.fancybox.pack.js
├── index.html
└── js
    └── script.js
```

- `index.html` 为网站首页。
- `js/` ：存放 hexo 的功能 js。
- `css/` ：存放 css 样式文件。
- `archives/` ：存放博客归档。
- [`fancybox`][] 是一款优秀的 [jquery 插件][]，它能够展示[丰富的弹出层效果][]。

## hexo server
上面通过 `init` 和 `generate` 之后，已经生成博客网站了。此时，只包含一个默认首页 hello-world.md。  

执行 `hexo server` 可启动 **hexo server** 进行本地调试：

```Shell
➜  blog  hexo server
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
^CINFO  Bye!
➜  blog  
```

在浏览器中输入 `http://localhost:4000` 即可访问只含欢迎页和默认主题的 hexo 博客了。  
![hexo-hello_world](http://7xo5uz.com1.z0.glb.clouddn.com/hexo-hello.png)

按下 `Ctrl+C` 退出 hexo 服务器。

## hexo deploy
### 配置 Deployment
编辑站点配置文件(`/_config.yml`)里面的 Deployment 发布配置。  
其中 deploy type 修改为 `github`；repository 修改为自己在 Github 上的 User Pages 的地址：`https://github.com/fan2/fan2.github.io.git` 。

```yml
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repository: git@github.com:fan2/fan2.github.io.git
  branch: master
```

**注意：**

1. Repository：url 必须是 SSH 形式的，不支持 HTTPS 形式，否则会出现错误：`is not a valid repositor URL!`。  
2. 使用 SSH url，如果电脑没有开放 SSH 端口，会致部署失败：

	> fatal: Could not read from remote repository.
	> Please make sure you have the correct access rights and the repository exists.

3. 如果是为一个项目制作介绍网站，那么需要把 branch 设置为 gh-pages 。

### hexo deploy
执行 `hexo deploy` ，将静态网站代码（public/*）上传到 github 上。

```Shell
➜  blog hexo deploy
ERROR Deployer not found: github
```

原因是在 hexo 3.0 之后, 不仅 github 的 deploy 的 type 从 github 改为了 git，其中的 deploy 功能也被单独做成一个模块，需要另外安装。所以我们需要安装 **hexo-deployer-git**。

**解决方案：**

1. [将 github 修改为 git][]， 还是报错；
2. [安装 hexo-deployer-git][]:

```Shell
➜  blog npm install hexo-deployer-git --save
 
> fsevents@1.0.5 install /Users/faner/Projects/git/blog/node_modules/hexo-deployer-git/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents
> node-pre-gyp install --fallback-to-build

[fsevents] Success: "/Users/faner/Projects/git/blog/node_modules/hexo-deployer-git/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents/lib/binding/Release/node-v46-darwin-x64/fse.node" is installed via remote

```

其中 `--save` 参数用于把模块的版本号添加到 package.json 文件中的依赖里（dependencies），否则在安装完之后需要手动添加。  
下载安装完成后，`node_modules` 下将新增目录 `hexo-deployer-git`。

此时，重新执行 `hexo deploy` ，提交发布成功！

```Shell
➜  blog hexo deploy                         
INFO  Deploying: git
INFO  Setting up Git deployment...
Initialized empty Git repository in /Users/faner/Projects/git/blog/.deploy_git/.git/
[master (root-commit) 578b537] First commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 placeholder
INFO  Clearing .deploy folder...
INFO  Copying files from public folder...



To git@github.com:fan2/fan2.github.io.git
 + 9a3d901...6fe0445 master -> master (forced update)
Branch master set up to track remote branch master from git@github.com:fan2/fan2.github.io.git.
INFO  Deploy done: git

```

**注意：**

1. 部署会覆盖掉你之前在版本库 `fan2.github.io` 中存放的文件。  
2. `hexo deploy` 时将在博客根目录下生成 `.deploy_git` 文件夹，下一次 `hexo deploy` 将会基于 diff 提交差异量。

## 写新文章，更新博客
新建博文，其中 postName 是博文题目：

```Shell
hexo new "postName"  
```

hexo 会自动在博客目录 `source/_posts` 下生成 postName.md 文件。

### front-matter
markdown 文件开头的 **front-matter** 属性（ `---` 上面的区域）中可以定义文章的属性，便于 hexo 主题模板生成格式化的文章。

以下是本文的 front-matter，其中定义了标题、日期、所属分类和多标签：

```
title: 基于 Hexo 和 GitHub Pages 搭建博客
date: 2015-11-12 19:27:00
categories: Hexo
tags:
- node.js
- npm
- hexo
- next
- feed
- analytics
- sitemap
```

以下是下一篇博文《git 版本控制》的 front-matter，其中定义了标题、日期、所属分类和多标签：

```markdown
title: git 版本控制
date: 2015-11-17 07:13:23
categories: git
tags: [git-init, git-add, git-commit, git-pull, git-push]
---
```

### 重新生成部署
每次修改更新本地博客源码文件后，需要针对该博客目录执行 `hexo generate` 重新（增量）编译；再键入 `hexo deploy` 即可上传到 Github 上。这两步也可合并为 `hexo d -g` ，先生成再部署。  
如果 SSH 被禁用了，建议手动将 `public/` 目录下的静态网站 git push 到 GitHub Pages 博客仓库上。

## 图片存储
在 `source` 下建立与博客 `blog.md` 同名的目录 `blog` ，用于存放图片等资源文件，首页相对引用成功；但点击进入文章，相对引用失败！  
可参照 [hexo 资源文件夹][hexo_resource] ，看看如何设置 asset 相对路径。也可采用[七牛][qiniu]等云存储平台做图床，使用绝对路径。

## 404
推荐使用[腾讯公益404][]。

新建 source/404.html，在 `<body>` 部分嵌入腾讯公益404的 js 代码：

```html
---
layout: default
---
<html>
    <head>
         <meta charset="UTF-8" />
         <title>404</title>                                                                                                                                        
    </head>
    <body>
         <script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8" homePageUrl="http://col.dog/" homePageName="返回 col.dog 主页"></script>
    </body>
</html>
```

修改“homePageUrl”、“homePageName”这两个参数即可定制返回链接。  
重新生成部署，即可在访问本站不存在的页面（资源）时显示腾讯公益404页面。

## 配置主题——[Next][]
在终端 cd 到博客站点目录 `Projects/git/blog/theme` 下，git clone 下载 NEXT 主题到本地目录 `themes/next` 下：

```Shell
➜  blog  git clone https://github.com/iissnan/hexo-theme-next themes/next
```

编辑站点配置文件，将字体从繁体切换为简体、将主题从默认的 landscape 切换为 next：

```yml
# Site
language: zh-Hans #zh-hk

# Extensions
## Themes: http://hexo.io/themes/
theme: next # landscape
```

重新生成部署网站，将按 next 主题风格排版布局站点。

### 开启 Mist
编辑主题配置文件(`/themes/next/_config.yml`) ，开启 NexT.Mist 风格主题：

```yml
# Schemes
scheme: Mist
```

### 配置菜单
编辑主题配置文件里面的 menu 项，配置顶部菜单：

```yml
menu:
  home: /
  archives: /archives
  categories: /categories
  tags: /tags
  # commonweal: /404.html
  about: /about
```

1. home: /，**首页**，点击将显示根目录 /index.html 。
2. archives: /archives，**归档**，点击将显示文章列表。
3. categories: /categories，**分类**，点击将显示文章分类。
4. tags: /tags，**标签**，点击将显示文章标签。
5. about: /about，**关于**，介绍一下自己。

### [创建分类页面][]
默认点击访问 分类(Categories) 链接，将会提示 404，找不到页面。

1. 新建一个页面，命名为 categories 。命令如下：

```Shell
hexo new page categories
```

2. 编辑刚才新建的页面(`/source/categories/index.md`)，将页面的类型（type）设置为 categories ，主题框架模板将自动在这个页面显示所有分类。

在该 markdown 文件中，只需要定义文章开头的 **front-matter** 属性（文件最上方以 `---` 分隔的区域），正文为空:

```markdown
title: 分类
type: "categories"
comments: false
---
```

重新生成部署生效。

**注意：**

> 下文有启用 多说 或者 Disqus 评论，模板生成的分类页面也会带有评论。这里添加字段 comments ，并将值设置为 false ，关闭分类页面的评论。

### [创建标签页面][]
默认点击访问 分类(Tags) 链接，将会提示 404，找不到页面。

1. 新建一个页面，命名为 tags 。命令如下：

```Shell
hexo new page tags
```

2. 编辑刚才新建的页面(`/source/tags/index.md`)，将页面的类型（type）设置为 tags ，主题框架模板将自动在这个页面显示所有分类。

```markdown
title: 标签
type: "tags"
comments: false
---
```

- 标签页面也关闭了评论，重新生成部署生效。
- 多标签的两种书写格式参照 **front-matter** 章节。

### [创建“关于我”页面][]
Hexo默认不生成 About 页面，有需要的话可以创建一个叫 about 的 page，然后再添加到菜单项。

在命令行里面输入：

```Shell
hexo new page "about"
```

然后，你会发现 source 里面多了个 about 目录，里面有个 index.md。其实你也可以手动建立 markdown 文件，页面的格式和文章一样。

## [添加多说评论][]
使用社交账号登录 [多说][] 网站，创建一个站点。具体步骤如下：

1. 登录后在首页选择 “我要安装”。
2. 创建站点，填写站点相关信息。注意，**多说域名** 这一栏填写的即是你的 duoshuo_shortname。
3. 编辑主题配置文件，添加 duoshuo_shortname 字段，设置如下：

```yml
duoshuo_shortname: your-duoshuo-shortname
```

**注意：**

> duoshuo short name: 你的多说二级域名为去掉 `.duoshuo.com` 的前缀部分。

## [添加 RSS/Feed][]
很多新闻网站和 CMS 都会生成 atom 格式的 xml 文件（atom.xml），以供 RSS 订阅软件或聚合网站读取。这个在前几年是一种主流的新闻分享方式，聚合网站通过远程读取 atom 文件的内容，将内容通过指定的格式输出在网站上，以供大家浏览。

1.在命令行终端中，通过 npm 来安装 [hexo-generator-feed][] 插件：

```Shell
➜  github-pages git:(master) ✗ npm install hexo-generator-feed --save
hexo-generator-feed@1.0.3 node_modules/hexo-generator-feed
├── object-assign@3.0.0
└── ejs@1.0.0
```

2.在站点配置文件中加载 hexo-generator-feed 插件：

```yml
Plugins:
- hexo-generator-feed
```

3.在站点配置文件中配置 [Atom XML 聚合][] 格式的 feed：

```yml
#Feed Atom
feed:
  type: atom
  path: atom.xml
  limit: 20
```

主题配置文件中的 `rss` 字段的值保持为空，以便[显示 feed 链接][]。  
重新编译网站，将在 `public` 目录下生成 `atom.xml` 文件。用户点击博客右侧边栏中的 `RSS` 图标，即可访问 <http://col.dog/atom.xml> 。

## 添加统计分析（Analytics）
### [hexo/next 添加 Google/百度 统计][]
[Google Analytics（分析）][Google-Analytics] 不仅可以帮助您衡量销售与转化情况，而且能为您提供新鲜的深入信息，帮助您了解访问者如何使用您的网站，他们如何到达您的网站，以及您可以如何吸引他们不断回访。

[百度统计][Baidu-Analytics] 是百度推出的一款免费的专业网站流量分析工具，能够告诉用户访客是如何找到并浏览用户的网站，在网站上做了些什么，有了这些信息，可以帮助用户改善访客在用户的网站上的使用体验，不断提升网站的投资回报率。

到 Google 或 Baidu 的统计分析门户注册自己的站点，将获得统计分析平台为你的站点分配的唯一的网站统计 ID 。

编辑主题配置文件，新增 google_analytics 或 baidu_analytics 字段（取决于使用的统计系统），其值为申请到的统计 ID，即可将自己的站点接入 Google / Baidu 的统计分析平台：

```yml
google_analytics: your-analytics-id
baidu_analytics: your-analytics-id
```

下面基于 hexo/next 博客，阐述如何接入腾讯统计分析。

### hexo/next 添加 腾讯分析
#### 为自己的站点申请腾讯分析 ID
1.进入 [腾讯分析首页][]，使用 QQ 账号登陆。
2.登录后，进入 [腾讯分析欢迎页][] ，提示“您还没有注册站点，请先添加您的站点”，需要在编辑框中输入域名或二级域名，这里添加 `col.dog`。
3.请将下列代码添加至网站代码 `</body>` 前（参见下一节的安装说明）。

```html
<script type="text/javascript" src="http://tajs.qq.com/stats?sId=$your_ta_sid" charset="UTF-8"></script>
```

其中 `$your_ta_sid` 为腾讯分析为你注册的网站分配的 ID。

**注意：**

> 框架式网站请在框架集页面和子框架页面均安装统计代码，框架集页面请安装在 `</head>` 前。

#### 将自己的站点接入腾讯分析
1.在目录 `/themes/next/layout/_scripts/analytics/` 下新建 `tencent-analytics.swig` 文件，基于 `baidu-analytics.swig` 略作修改：

```swig
{% if theme.tencent_analytics %}
  <script type="text/javascript">
    var _hmt = _hmt || [];
    (function() {
      var hm = document.createElement("script");
      hm.src = "//tajs.qq.com/stats?sId={{ theme.tencent_analytics }}";
      var s = document.getElementsByTagName("script")[0];
      s.parentNode.insertBefore(hm, s);
    })();
  </script>
{% endif %}
```

以上代码的意思是，如果 theme 配置了 `tencent_analytics` ID，则添加腾讯分析的 `<script>` 统计代码。

2.打开 `/themes/next/layout/_scripts/analytics.swig` 文件，在末尾添加包含 `tencent-analytics.swig` 文件：

```swig
{% include 'analytics/tencent-analytics.swig' %}
```

3.打开主题配置文件，新增配置字段 `tencent_analytics`，设置其值为 `your_ta_sid`。

```yml
tencent_analytics: $your_ta_sid
```

4.重新生成部署网站，则可接入腾讯分析。

在 腾讯分析 网站的个人站点列表中可以浏览网站概况，其中列出了 **今日浏览量（PV）**、**今日独立 IP**、**今日用户量（UV）** 等统计分析指标。

## 提交搜索引擎收录（SiteMap）
站点地图（SiteMap）有助于搜索引擎更好地决定如何抓取您的网站，进而提高博客站点在搜索结果中的曝光率，从而[促进博客的分享推广][]。

1.在命令行终端中，通过 npm 来安装 [hexo-generator-sitemap][] 插件：

```Shell
➜  github-pages git:(master) ✗ npm install hexo-generator-sitemap --save
hexo-generator-sitemap@1.0.1 node_modules/hexo-generator-sitemap
├── utils-merge@1.0.0
└── ejs@1.0.0
```

2.在站点配置文件中加载 hexo-generator-sitemap 插件：

```yml
Plugins:
- hexo-generator-sitemap
```

3.在站点配置文件中设置 sitemap 的路径（path）为 sitemap.xml：

```yml
#sitemap
sitemap:
  path: sitemap.xml
```

重新编译网站，将在 `public` 目录下生成 `sitemap.xml` 文件。

使用 Google/Gmail 账号登录 [Google Search Console][] ，注册自己的站点，验证身份（google_site_verification）后，可以指定博客站点的地图文件（/sitemap.xml）。之后，Google 搜索引擎会自动定时获取站点最新的 sitemap.xml 文件。

## 参考
[Hexo 系列攻略](http://ijiaober.github.io/categories/hexo/)  
[Hexo 系列教程](http://zipperary.com/categories/hexo/)  
[hexo 你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)

[Hexo github 独立博客](http://cnfeat.com/blog/2014/05/10/how-to-build-a-blog/)  
[创建GitHub技术博客全攻略](http://blog.csdn.net/renfufei/article/details/37725057/)

[用 HEXO 搭建个人博客](http://andy-yao.me/2015/05/06/blog-with-hexo/)  
[搭建你自己的 Hexo 博客](http://blog.newbmiao.com/2015/06/16/hexo-on-your-github.html)  
[简明 Github Pages 与 Hexo 教程](http://blog.csdn.net/poem_of_sunshine/article/details/29369785/)

[Hexo 3.0 静态博客使用指南](http://segmentfault.com/a/1190000002592993)  
[Hexo 搭建 Github 静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)  
[Hexo 免费静态博客安装和使用方法](http://www.freehao123.com/hexo-node-js/)  
[Hexo 在 github 上构建免费的 Web 应用](http://blog.fens.me/hexo-blog-github/)

[hexo 主题优化](http://www.voidking.com/2015/05/31/deve-hexo-theme-optimize/)  
[Hexo 博客优化](http://www.bubuko.com/infodetail-618287.html)  
[hexo 的私人订制](http://blog.sunnyxx.com/2014/03/07/hexo_customize/)  
[Hexo 优化与定制](http://lukang.me/categories/Hexo/)

[关于 Hexo 的若干问题](http://bubkoo.com/2013/12/16/hexo-issure/)  
[Hexo常见问题解决方案](https://xuanwo.org/2014/08/14/hexo-usual-problem/#Hexo3-0%E4%BB%A5%E4%B8%8A%E6%94%AF%E6%8C%81)

## <!--以下是本文的脚注和超链接-->

<!-- ## 安装 Node.js + NPM -->

[nodejs-cn]:http://nodejs.cn/
[Chrome V8]:https://developers.google.com/v8/
[npm]:https://www.npmjs.com/
[维基百科]:http://en.wikipedia.org/wiki/Nodejs
[Node.js API]:http://nodeapi.ucdok.com/
[Node.js 究竟是什么？]:http://www.ibm.com/developerworks/cn/opensource/os-nodejs/
[为什么要用 Node.js ?]:http://blog.jobbole.com/53736/
[使用 Node.js 的优势和劣势都有哪些？]:http://www.zhihu.com/question/19653241
[谁在使用 Node.js ?]:https://github.com/nodejs/node-v0.x-archive/wiki/Projects,-Applications,-and-Companies-Using-Node
[nodejs-en]:https://nodejs.org/
[Homebrew]:http://brew.sh/index.html
[Mac 上安装 Homebrew]:http://blog.csdn.net/delphiwcdj/article/details/19679891
[使 OS X 更完整]:http://blog.csdn.net/aaawqqq/article/details/44088141
[RubyGems]:http://www.cnblogs.com/ihojin/p/ruby-gem-update-the-latest-version.html
[NPM 的网站]:https:://npmjs.org/
[静态博客框架]:http://code.csdn.net/news/2819909
[Hexo]:https://hexo.io/

<!-- ## hexo init -->

[ls-tree-mac]:http://www.aikaiyuan.com/5413.html

<!-- ## hexo generate -->

[fancybox]:http://doc.bropaul.com/fancyBox/
[jquery 插件]:http://www.cnblogs.com/zhmt/archive/2011/10/06/2200152.html
[丰富的弹出层效果]:http://www.helloweba.com/view-blog-65.html

<!-- ## hexo deploy -->

[将 github 修改为 git]:http://www.v2ex.com/t/175940
[安装 hexo-deployer-git]:http://blog.163.com/gis_warrior/blog/static/19361717320153100184696/

<!-- ## 图片存储 -->

[hexo_resource]:https://hexo.io/zh-cn/docs/asset-folders.html
[qiniu]:http://www.qiniu.com/

<!-- ## 404 -->

[腾讯公益404]:http://www.qq.com/404/

<!-- ## 配置主题 -->

[Next]:https://github.com/iissnan/hexo-theme-next
[创建分类页面]:https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA%E6%A0%87%E7%AD%BE%E4%BA%91%E9%A1%B5%E9%9D%A2
[创建标签页面]:https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA%E5%88%86%E7%B1%BB%E9%A1%B5%E9%9D%A2
[创建“关于我”页面]:https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA-%22%E5%85%B3%E4%BA%8E%E6%88%91%22-%E9%A1%B5%E9%9D%A2

<!-- ## 添加多说评论 -->

[添加多说评论]:https://github.com/iissnan/hexo-theme-next/wiki/%E8%AE%BE%E7%BD%AE%E5%A4%9A%E8%AF%B4-DISQUS
[多说]:http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9

<!-- ## 添加 RSS/Feed -->

[添加 RSS/Feed]:https://github.com/xiangming/landscape-plus/issues/31
[hexo-generator-feed]:https://github.com/hexojs/hexo-generator-feed
[Atom XML 聚合]:http://www.w4s.cn/feed/atom
[显示 feed 链接]:https://github.com/iissnan/hexo-theme-next/wiki/%E6%98%BE%E7%A4%BA-feed-%E9%93%BE%E6%8E%A5

<!-- ## 添加统计分析 -->

[hexo/next 添加 Google/百度 统计]:https://github.com/iissnan/hexo-theme-next/wiki/%E6%B7%BB%E5%8A%A0-Google---%E7%99%BE%E5%BA%A6-%E7%BB%9F%E8%AE%A1
[Google-Analytics]:https://www.google.com/analytics/
[Baidu-Analytics]:http://tongji.baidu.com/
[腾讯分析首页]:http://v2.ta.qq.com/analysis/index
[腾讯分析欢迎页]:http://v2.ta.qq.com/bind/site

<!-- ## 提交搜索引擎收录 -->

[促进博客的分享推广]:http://aaronyee.com/blog/2015/06/14/blog-open.html
[hexo-generator-sitemap]:https://github.com/hexojs/hexo-generator-sitemap
[Google Search Console]:https://www.google.com/webmasters/tools/home?hl=zh-CN
