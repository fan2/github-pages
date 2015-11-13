title: 基于 Hexo 和 GitHub Pages 搭建博客
date: 2015-11-12 19:27:00
categories: Hexo
tags: 博客志
---

本文详细记录了从安装 `node.js` / `Hexo` 到 使用 `hexo` 发布 markdown 格式的博客并托管到 GitHub 的全过程。

<!--more-->

## 安装 node.js

> Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine][]. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, [npm][], is the largest ecosystem of open source libraries in the world.

执行 `brew install node` 命令使用 brew 安装 node，提示不能创建 `/usr/local/Cellar` 目录，原因是没有 `/usr/local` 的写权限。

**[解决方法][brew_install_write_permission_issue]：**

> 执行 `chown` 命令改变 `/usr/local` 的所有者为 `whoami` ，`-R` 表示递归（Recursive）。

```Shell
➜  ~  brew install node
Error: Could not create /usr/local/Cellar
Check you have permission to write to /usr/local

➜  ~  sudo chmod -R g+w /usr/local
Error: Could not create /usr/local/Cellar
Check you have permission to write to /usr/local

➜  ~  whoami
faner

➜  ~  sudo chown -R faner /usr/local
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

执行 `node -v` 命令查看安装的 node.js 版本：

```Shell
➜  ~  node -v
v4.2.1
```

## 安装 NPM
NPM（Nodejs Packager Manager），是 Node.js 包管理器。

在安装新版的 `node.js` 时，包中已经集成了 npm 包管理工具。  
可执行 `npm -v` 验证版本：

```Shell
➜  ~  npm -v
2.14.7
```

执行 `npm help` 命令可查看帮助。

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

## NPM 安装 hexo
### npm install hexo

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

> `-g` 表示全局安装, npm默认为当前项目安装。

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

在线帮助文档：http://hexo.io/docs/

- hexo g == hexo generate
- hexo d == hexo deploy
- hexo s == hexo server
- hexo n == hexo new

## hexo init
假设我们接下来将在本地工程目录 `Projects/git/blog` 下新建博客站点（WebSite）。  
cd 进入 `Projects/git/blog` 目录，针对该目录执行 `hexo init` 命令。

```Shell
➜  ~  cd Projects/git
➜  git  mkdir blog
➜  git  cd blog
➜  blog  hexo init
INFO  Copying data to ~/Projects/git/blog
INFO  You are almost done! Don't forget to run 'npm install' before you start blogging with Hexo!
➜  blog  ls
_config.yml  package.json scaffolds    source       themes
```

`hexo init` 初始化成功后，可调用 `ls` 或 [`tree`][ls-tree-mac] 命令查看，hexo 在 blog 目录下生成了模板文件。

文件/目录     | 作用
-------------|-------------------------
package.json | 配置依赖包
_config.yml  | 全局的配置文件
source       | 存放博客源码（markdown文件）
themes       | 存放主题皮肤的目录


`./source/_posts` 目录下默认有一个欢迎页 —— `hello-world.md` 文件。

根据 `hexo init` 的下一步提示，需要执行 `npm install` 安装 package.json 里的依赖包。

```
➜  blog  hexo generate
ERROR Local hexo not found in ~/Projects/git/blog
ERROR Try running: 'npm install hexo --save'

➜  blog  npm install

➜  blog  ls
_config.yml  node_modules package.json scaffolds    source       themes

```

`ls` 查看目录下将多出 `node_modules` 文件夹，其中存放安装的依赖包。

## hexo generate
上面，我们在执行 `hexo init` 后，再执行 `npm install` 安装了 package.json 里所依赖的包。此时，执行 `hexo generate` 命令将编译（只有欢迎页：`hello-world.md`）生成待发布的文件。

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
生成的 `public` 文件夹存放编译后的静态网站（html+css+js），也就是最后要发布的文件夹。

在 public 目录执行 `tree -L 2`，可以查看其目录结构：

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

其中 `index.html` 为网站首页。

## hexo server
上面通过 `init` 和 `generate` 之后，已经生成网站了。此时，只包含一个默认首页 hello-world.md。  

执行 `hexo server` 可启动 hexo server 进行本地调试：

```Shell
➜  blog  hexo server
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
^CINFO  Bye!
➜  blog  
```

在浏览器中访问 `http://localhost:4000` 可访问只含欢迎页和默认主题的 hexo 博客了。  
![hexo-hello_world](http://7xo5uz.com1.z0.glb.clouddn.com/hexo-hello.png)

按下 `Ctrl+C` 关闭退出 hexo 服务器。

## hexo deploy
### 配置 Deployment 
修改 `_config.yml` 文件里面的 Deployment 发布配置。  
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
1. Repository：url必须是SSH形式的，而不能是HTTPS形式，否则会出现错误：`is not a valid repositor URL!`。
2. 使用SSH url，如果电脑没有开放SSH 端口，会致部署失败：

	> fatal: Could not read from remote repository.
	> Please make sure you have the correct access rights and the repository exists.

3. 如果是为一个项目制作介绍网站，那么需要把branch设置为gh-pages。

### hexo deploy
执行 `hexo deploy` ，将静态网站代码（public）上传到 github 上。

```Shell
➜  blog hexo deploy
ERROR Deployer not found: github
```

**解决方案：**

1. [将 `github` 修改为 `git`](http://www.v2ex.com/t/175940)， 还是报错；
2. [安装 `hexo-deployer-git`](http://blog.163.com/gis_warrior/blog/static/19361717320153100184696/), 

```Shell
➜  blog npm install hexo-deployer-git --save
 
> fsevents@1.0.5 install /Users/faner/Projects/git/blog/node_modules/hexo-deployer-git/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents
> node-pre-gyp install --fallback-to-build

[fsevents] Success: "/Users/faner/Projects/git/blog/node_modules/hexo-deployer-git/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents/lib/binding/Release/node-v46-darwin-x64/fse.node" is installed via remote

```

下载安装完成后，`node_modules` 下将新增目录 `hexo-deployer-git`。

此时，重新执行 `hexo deploy` 成功！

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

注意部署会覆盖掉你之前在版本库 `fan2.github.io` 中存放的文件。

## 图片存储
在 `source` 下建立与博客 `blog.md` 同名的目录 `blog` 其中存放图片文件，最终首页相对引用成功，点击进入文章，相对引用失败！
可参照 [hexo 资源文件夹][hexo_resource] ，看看如何设置 asset 相对路径。或者采用[七牛][[qiniu]]做图床，使用绝对路径。

## 更新博客
新建博文，其中 postName 是博文题目：

```Shell
hexo new "postName"  
```

hexo会自动在博客目录 `source/_posts` 下生成 postName.md 文件。

每次修改更新本地博客源码文件后，需要在该路径键入 `hexo generate` 才能保存。再键入 `hexo deploy` 即可上传到 Github 上。如果 SSH 被禁用了，建议手动将 public 静态网站 push 到博客仓库上。

## <!--以下是本文的脚注和超链接-->

[Chrome's V8 JavaScript engine]:https://developers.google.com/v8/
[npm]:https://www.npmjs.com/
[brew_install_write_permission_issue]:http://segmentfault.com/q/1010000000505091
[ls-tree-mac]:http://www.aikaiyuan.com/5413.html
[hexo_resource]:https://hexo.io/zh-cn/docs/asset-folders.html
[qiniu]:http://www.qiniu.com/
