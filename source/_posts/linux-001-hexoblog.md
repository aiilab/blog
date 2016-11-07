---
title: 如何用Hexo+Github搭建个人独立博客，并实现多台电脑同步?
date: 2016-11-01 12:00:00
tags:
    - Hexo
    - Linux 
    - Github
header-img: /img/post-default.jpg
---

## 如何优雅的搭建个人独立博客？
## 前言
作为一个前端小白（其实，我是一个[算法工程师](http://blog.aiilab.com/about/)），想自己搭个博客来玩一下。主要是最近突然想起了一个觉得屌炸天的域名——人工智能创新实验室（[www.aiilab.com](www.aiilab.com)）。遂折腾了一下，以此写写博客，纪录自己在AI领域的拙见，与同行多多交流。所以简单总结了一下自己从申请域名到发布博客文章的流程，并实现多台电脑同步。
## 基本流程
先说一下我的情况，两台终端，一台是公司电脑安装了Linux系统（ubuntu14.04），一台是自己的Macbook pro。最终实现两台电脑同步控制。比如，在公司或者实验室电脑上写了一半博客，回到家或宿舍后又想继续写并发布，或者继续修改也ok，很方便。

一般来讲，要想实现通过一个个人网站来访问博客**流程**如下：
>* 首先，你得有个域名。比如，我自己申请的*[www.aiilab.com](aiilab.com)*。申请网站有很多，比如[godaddy](http://www.godaddy.com)、[万网](http://www.net.cn)、[西部数码](http://www.west.cn)等。本人是从西部数码申请的，价格都差不多。说真心的，现在申请个好域名真难，好多都被人注册了。据说，360为了买www.360.com花了8为数!!! 呵呵～
>* 其次，有了域名那你得有个服务器放你的博客。所以得有一个服务器的地址。当你在浏览器输入比如www.aiilab.com的时候，会将这个域名映射到你博客的服务器上去。那域名咋映射到服务器上呢，这就需要域名解析，就是常听到的DNS，不过不用担心，这个只需要在你申请域名的网站上设置一下CNAME就ok，CNAME其实就是别名，设置好CNAME后，当你在浏览器输入域名后，就可以找到（映射）你博客所在的服务器的地址了。
>* 刚刚说了得有一个服务器放你的博客上去，一般国内服务器你可以用阿里的，但是有个问题，要钱！而且，党国规定，要想用国内的服务器就得到有关部门备案，否则，呵呵～。但是，机智的程序员们发现了可以用GitHub Pages的功能来搭建博客，Github Pages是啥玩意儿待会儿细说，说简单点呢，它就是放你博客的服务器。你只需要按它的规定把你的博客网页放到github上面就哦了。用github有两个好处。第一，服务器在国外，不用备案。第二，空间免费，不用花钱。另外，如果不想申请域名直接输入你的博客网页在github上的地址就行，如果自己申请了域名，那直接设置一下CNAME，将你的域名和你的博客github地址关联起来就ok。
>* 等等，啥博客网页？难道要自己写网页？要骂人了，尼玛不会啊！当然是No！有个台湾的程序员叫Tommy Chen，他写了一个叫[Hexo](https://hexo.io)的工具，分分钟就可以把你的写的博客转化成浏览器能够解析的网页，官方术语为：Hexo 是一个简单地、轻量地、基于Node的一个静态博客框架，可以方便的生成静态网页。
>* 基于上述情况，便可用Hexo+Github来搭建个人独立博客。

纳尼，github也没听过？竟然连程序员交友网站都没有听过，那你肯定有女（男）朋友！如果没有用过github，那小小白用户需要了解一下以下内容：
>* [git](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000) 
>* [github pages](https://pages.github.com) 
>* [hexo](https://hexo.io) 
>* [Markdown](http://www.appinn.com/markdown/#autoescape)

废话说了这么多，目的是让小白用户能有一个大概的认识，接下来就一步一步实际操作就ok。
## Step 1. 域名申请及配置
### 域名申请及CNAME配置
这部分就比较简单了，我在西部数码买的，输入想要买的域名，确定没被别人注册之后便可付款购买。购买完以后需要对其进行设置，以我申请的aiilab.com域名为例，其域名解析设置如下：
![dns](http://cdnshaw.hosea.pw/linux-001-hexoblog/dns2.jpg)
可以先按照图片当中的进行设置，但是需要将`aiilab`改成你申请的域名，即`你的域名.github.io`。下面简单介绍下域名解析各种设置是啥意思(以我的域名为例)：
>* www表示，我想输入www.aiilab.com进行访问
>* @表示直接解析主域名，即`aiilab.com`。意思就是当你在浏览器输入aiilab.com的时候，不用输入www.也可以直接访问。即跳转到`aiilab.github.io`(即github放博客服务器的地址)
>* \*表示泛域名解析，即`*.aiilab.com`。意思就是无论你在浏览器输入`xxx.aiilab.com`的时候，它都会跳转到你CNAME对应的`aiilab.github.io`上去。比如，你随便输入`blog.aiilab.com`，就会将这个定向到aiilab.github.io.
>* blog 表示我想通过blog.aiilab.com来访问我的博客

**注意注意注意**：不要设置泛解析，否则任何人都可以用你的子域名来做博客了。比如，别人在人家的github账号下建了一个叫music的工程，CNAME设置为music.aiilab.com。那在浏览器输入music.aiilab.com的时候尼玛竟然跑到人家的博客去了，\*.aiilab.com都是我的域名好不好！！！为啥呢，因为路径是这样的，music.aiilab.com->\*.aiilab.com->aiilab.github.io->(然后在所有github工程里面去找CNAME设置为music.aiilab.com的那个工程，当然就找到别人家去了)
*Danger: Do not use wildcard DNS records (e.g. \*.example.com) with GitHub Pages! A wildcard DNS record will allow anyone to host a GitHub Pages site at one of your subdomains.*

先设置这两个就ok。好，第一步结束！
### github解析博客原理(可以跳过)
为了更好的说明github是如何解析github pages的，现在我们来做个实验，首先按照上图设置CNAME即打开终端，依次输入：
``` bash
dig www.aiilab.com +nostats +nocomments +nocmd
dig aiilab.com +nostats +nocomments +nocmd
dig blog.aiilab.com +nostats +nocomments +nocmd
dig aiilab.github.io +nostats +nocomments +nocmd
dig dhyyag.github.io +nostats +nocomments +nocmd
dig bvlc.github.io +nostats +nocomments +nocmd
```
如下图所示：可以看到
![dig](http://cdnshaw.hosea.pw/linux-001-hexoblog/dig2.jpg)
>* www.aiilab.com、aiilab.com和blog.aiilab.com已经映射到aiilab.github.io，用的是CNAME，即别名
>* \*.github.io　即所有的\*.github.io都映射到github.map.fastly.net，用的是CNAME，即别名
>* 而github.map.fastly.net最终映射到了一个IP地址，即151.101.16.133(也有可能是151.101.*.133）. 直接映射到IP为A记录。
>* 最后一点很重要，分两部分：
１、当你在浏览器输入xxx.github.io的时候，此时映射到最终的一个IP地址151.101.16.133，拿到的域名为xxx.github.io，此时由这个IP地址来去找CNAME为xxx.github.io的github项目(CNAME会映射为相应的域名)。然而xxx.github.io是唯一的，所以不需要CNAME也ok。
２、当你输入www.aiilab.com、aiilab.com、blog.aiilab.com这些域名时，(把这些地址想象为快递)最终都将映射到同一个IP地址(当成中转站)。然后由这个中转站去解析github所有工程里面CNAME对应的域名和输入的域名相同的github peoject　(CNAME和github project项目一一对应)。哎呀，妈呀，太费劲了。上个图来说吧。
![gitdns](http://cdnshaw.hosea.pw/linux-001-hexoblog/gitdns.jpg)
例如，我的github里面有一个名为blog的项目，其中gh-pages分支放我的博客内容。我将gh-pages分支里的CNAME设置为blog.aiilab.com。当你输入blog.aiilab.com的时候，DNS会解析为aiilab.github.io,然后最终会映射到IP为151.101.*133的中转站，然后从CNAME表中去查询输入的域名，即blog.aiilab.com，发现一个叫blog的项目的CNAME为刚好好输入的域名相同，那就直接解析blog这个项目gh-pages的对应的博客网页，就可以看到博客内容了。

## Step 2. git及github设置
### 什么是git?
git是什么鬼？[Git是目前世界上最先进的分布式版本控制系统（没有之一）](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。这块我就不多数了，有很多很好的教程和博客，在此推荐[廖雪峰](http://www.liaoxuefeng.com/)写的git教程，写的非常棒，堪称史上最浅显易懂的Git教程！强烈推荐！
### 什么是github?
[GitHub](https://github.com/) 是一个面向开源及私有软件项目的托管平台，因为只支持 Git 作为唯一的版本库格式进行托管，故名 GitHub。其实，我觉得Github才是程序员专业的社交平台。利用该平台，程序员可以托管自己的各种项目，装扮自己的空间，如果项目好，就会有很多人给你点赞(star)， 遇到有啥问题，还可以在项目里留言，会有大神为你解答，如果有你感兴趣的项目，你还可以提交代码，做贡献。总之，git功能强大，而github连接了全世界的程序员。当然，github托管开源项目是不收费的，我们的博客一般都是为了让别人看，所以肯定是开源的。私人仓库（托管自己项目的地方）是收费的，所以出现了很多类似github网站的东东。比如公司里用的gitlab、国内还有gitcafe等，套路都是一样的。
### 什么是github pages?
废话少说，先注册一个[GitHub](https://github.com/)账号再说，注册很简单，我的注册用户名是aiilab。接下来大概说说[Github Pages](https://pages.github.com)是啥东东？其实，Github Pages就是github的网页呗，刚刚说了Github不是交友平台么，那你不得整个网页介绍一下你自己(或你的组织)怎么怎么牛逼么？所以User/Organization Page就是来干这事的。因为一个账号对应一个用户，所以User Page只能有一个。一般来讲只要将你的展示页面放到username.github.io这个项目的master分支下，就可以用`http(s)://username.github.io`来访问你的博客了，比如输入[http(s)://aiilab.github.io](http://aiilab.github.io)就可以访问我的博客了，因为我设置了CNAME，所以跟输入[www.aiilab.com](http://www.aiilab.com)是一样一样的。 另外，github 不是可以托管项目么，那你不得整个网页介绍下项目么，所以Project Pages就是用来展示你的项目的。当然项目有很多个，所以Project Pages可以有多个，所以你也可以将你的博客页面放到`projectname`这个项目的`gh-pages`分支下，就可以用`http(s)://username.github.io/projectname`来访问你的博客了。比如，我在github上建立一个blog项目，其中gh-pages分支用来放博客的内容。所以也可通过[http(s)://aiilab.github.io/blog](http://aiilab.github.io/blog)来访问我的博客。简单总结一下：
>* GitHub Pages有两种类型：User/Organization Pages 和 Project Pages
>* User Pages 是用来展示用户的，而 Project Pages 是用来展示项目的。
>* User Pages 将使用仓库的 master 分支作为页面展示，而 Project Pages 将使用 gh-pages 分支作为页面展示。
>* User Pages 通过 `http(s)://username.github.io` 进行访问，而 Projects Pages 则通过`http(s)://username.github.io/projectname`来进行访问。   

我们将同时利用这两种pages进行博客的设置。
### git及github项目配置
前面文字太多，后面操作我就废话少说一点。以下适用于linux系统安装git,　windows用户建议用cygwin, 也可以安装[github for windows](http://desktop.github.com), mac用户建议用homebrew安装，具体请百度。这里，我们要区分清楚git与github。git是一个版本控制的工具，而github有点类似于远程仓库，用于存放用git管理的各种项目。linux下git配置操作步骤如下：
``` bash
cd ~
sudo apt-get install git  //　安装git
git config --global user.name "username" // 我的为git config --global user.name "aiilab"
git config --global user.email "username@example.com"//我的为 "aiishaw@126.com"
ssh-keygen -t rsa -C "your_email@example.com"  //配置ssh, 运行这行代码后一路回车
```
用户主目录下，有一个.ssh文件夹，在.ssh目录下， id_rsa 和id_rsa.pub就是ssh key 的密钥对在github 账户设置里添加SSH key, 在key文本里粘贴id_rsa.pub文件的内容。即
```
gedit ~/.ssh/id_rsa.pub
```
然后把打开文件里面的内容添加到github->Settings->SSH GPG keys->New SSH key里即可。

接下来分别在github建立两个项目。分别为：
>* 创建名为youname.github.io仓库 ，我的为 aiilab.github.io。添加一个CNAME文件, 里面的内容为`www.你的域名.com`，我的为`www.aiilab.com`。再添加一个名为index.html的文件，里面的内容是：
```html
<head>
    <!-- 以下方式定时转到其他页面 -->
    <meta http-equiv="refresh" content="0;URL=http://blog.你的域名.com"> 
</head>
```
即当输入www.你的域名.com的时候，先解析aiilab.github.io的master(默认)分支的页面，此时只有一个index.html。然后自动跳转到http://blog.你的域名.com。
>* 创建一个名为blog的仓库, 并新建一个gh-pages分支，添加一个CNAME文件，添加内容为`blog.你的域名.com`，我的为`blog.aiilab.com`

即将博客仓库放在了一个project　pages当中，这样方便管理。比如，你想用music.aiilab.com、photos.aiilab.com的时候都可采用类似方法。

## Step 3. Hexo 安装及配置
利用nvm安装Node.js
```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/master/install.sh | sh
nvm install stable
```
或者，通过[installer](http://nodejs.org/)安装Nohe.js，上述两个方法选一种就ok。

安装Hexo
```bash
npm install -g hexo-cli
```
Hexo配置
```bash
cd <folder>
hexo init
sudo npm install -g hexo
npm install hexo-deployer-git --save
```
_config.yml配置
打开_config.xml,添加／修改配置：
```
public_dir: ../public //这就是编译产生的博客网页，这个文件夹里面的内容放到github的刚刚建立的blog仓库的gh-pages分支就ok,配置好以下命令，便可通过hexo d 一条命令实现部署
deploy:
  type: git
  repo: git@github:你的用户名/blog.git
  branch: gh-pages
```
Hexo编译
新建一个博客内容，用如下命令。会在souce/_post/产生一个\*.md的文件夹，然后用markdown语言编写你的博客内容
``` bash
hexo n "test"  // hexo new "test",建立一篇名为test的博客
```
```
hexo g // hexo generate 编译
hexo s // hexo server 本地模拟，打开http://localhost:4000查看博客效果，如果4000端口被占用，可以利用hexo s -p 4001
hexo d //部署到github, 可以通过www.你的域名.com进行访问了
```
至此，博客搭建完毕！
## Step 4. 多台电脑之间同步
>* 给github的blog仓库，建立两个分支，其实上述国产已经建立好。一个是`master`，一个是`gh-pages`。远端仓库blog的master分支放博客的源文档及各种主题配置，gh-pages分支用来存放生成的静态网页。具体操作如下：
``` bash
cd blog
git remote add origin git@github.com:你的用户名/blog.git //与远程仓库关联
git pull origin master //拉下远程master分支，与当前（master）分支合并
git add --all
git commit -m "add new blog"
git push -u origin master//推送自己的修改,　e.g. git push -u origin master:dev 表示将本地master分支上传至远端origin的dev 分支
```
>* 当在另外一台配好hexo的电脑上，直接clone下来，修改发布后提交本地修改到远程master分支就ok.
``` bash
git clone git@github.com:你的用户名/blog.git 
sudo npm install -g hexo
npm install hexo-deployer-git --save（记得，不需要hexo init这条指令）
```
然后修改、添加博客，然后再提交就ok。这样通过git的分支功能，就非常方便的实现了多台电脑之间的同步。

## Step 5. 优化部署及管理
>* 更快对域名解析服务可以用dnspod
>* 因为github服务器在国外，如果博客需要大量图片的话，可以把图片放到[七牛](http://portal.qiniu.com)上。然后七牛会生生一个图片外链，将博客当中用到的图片的链接用七牛生成的图片外链代替就ok，亲测速度有明显提升。
>* 如果要更换主题，网上有很多，只需要将主题放到blog/themes文件夹下即可。然后再重新配置一下_config.yml
>* MarkDown推荐用在线的[CMD MarkDown](http://www.zybuluo.com)，可以边写边看效果，很赞！
>* Windows用户基本类似，遇到问题基本百度，谷歌可以搞定。如果搞不定，有问题多交流！

## Step 6. Enjoy && PS
至此，博客折腾暂时完毕，开始你的博客之旅吧！但是需要申明的有以下几点：
> * 独立博客只是一个工具，希望大家还是把重心放在内容上

> * Github是程序员最好的基友网站，还是希望大家多贡献代码，好好维护这个社区，争取搞点高质量的东西在上面，不要被玩坏了

> * 由于搭建过程当中也参考了多个博客内容，如图片、部分文字等，非常感谢大家的贡献，在此不一一说明，如有侵权，请及时告知删除！

Enjoy!
