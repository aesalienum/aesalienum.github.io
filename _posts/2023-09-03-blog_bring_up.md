---
title: 基于github page 和 jekyll 的个人网站搭建
author: aesalienum
date: 2023-09-03 21:00:00 +0800
categories: [博客, 教程]
tags: [博客]
pin: true
math: true
mermaid: true
image:
---

本文介绍了搭建个人博客所需的一些基础知识，并完整介绍通过 Github Page 和 Jekyll 搭建个人博客的过程，同时发散了一些常用技术的简单介绍，包括互联网基础、git、docker、CICD等。

## 缘起：内容平台 or 个人博客

在“个人博客”这个词出现在你的脑海并占据重要分量之前，你也许考虑过自己是不是还有别的什么选择。

答案是肯定的，互联网在提升信息传递效率的同时，也大大降低了信息通信的门槛，宽阔的信道鼓励每个人积极发声，填充这一个全新的、贫瘠的世界。众多的店家在这个广袤的世界中争抢地盘，划分势力范围，有的把持社交入口，有的用小视频和推荐算法搭建护城河，还有的从知识问答的小绿洲迁徙到分享现编故事的大荒漠。

然而不论种种，需要理解的是，这仍是一片野蛮生长的土地，我们的选择依然很多。想要操作简便，代价通常是效果单一，千篇一律，甚至功能缺失；垂涎收益和曝光，那么通常需要费工夫运营、调研、找准受众投其所好。更甚者，报刊杂志虽然是费时费力，但也有内容质量背书等其他优势。等等。

那么究其种种，倘若在并无深入了解的情况下，你仍青睐“个人博客”，可能的原因无外乎三种：

- 希望能更好掌握自己的内容空间
- 希望多学习和掌握一些知识
- 有闲，喜欢折腾

不论是哪一种，希望本文能为你提供有关个人博客的基础知识，并提供一个个人博客初试的简单方案。

## 前世：个人博客网站如何工作？

### *一封信*

信息太过抽象，总是需要一个媒介来承载，例如纸笔，例如比特。

当你洋洋洒洒做出一篇文章，迫切地想与他人分享时，或多或少你会有所迟疑：他人是否能看懂我的思考？然而，当你尝试进一步深究，就会发现，遑论思考，就连你笔走龙蛇的一个个汉字在他人眼里会不会是没有意义的鬼画符，也不是一件理所当然、显然就能明确的事。纸笔绘制的意义在几千年的人文传承和社会进步中才逐渐明晰，尽管比特站在巨人的肩膀上，在短短几十年的时间里就具备了对纸笔的替代能力，然而其中细节的繁琐程度却也丝毫未见减少。

让我们将一切的封装都看作顺理成章，仅以一篇能够被人理解的网页博客为例，看看这种“年轻”的方式是如何运作的。页面的底层是二进制，最小单位是比特，8个比特组成字节，而好几个字节按编码协议（例如 UTF-8）被假定为我们语言中的基本要素，称为字符，字符能够组成有意义的语句。本来，精简的通信链路已经诞生了，但是为了以人类能接受的方式呈现，语句需要用额外的控制字段描述，来设定显示效果，或便于修改，或组织更多维度的信息（例如图像、视频等）。这类标记协议很多，有 txt（不存在标记），常见的 docx 和 pdf ，科研写作常用的 latex（接触过的读者对此可能会有更深体会）以及构成本博客页面的html。

网页中的大部分页面源文件都是 html ，除了历史的发展中的偶然之外，其本身的优秀品质也是促使其流行的重要因素。除此之外，在你在浏览器界面中看到这篇博客的时候，还有很多事情在此之前发生。包括我准备好了这封信，internet 协议将它送达你的电脑，浏览器拆开信封渲染画面，显示驱动程序将之逐点扫描到你的显示器中。

### *邮局*

文本标记文件只是一封信，计算机/互联网系统在构建整个生态时，所做的事情远不止此。计算机在本地进行文件处理，孜孜不倦地在二进制、代码、自然语言、物理转换等方面做着翻译工作，互联网则通过程序和实际电路来连接不同的计算机，电路连接负责传输二进制符号，程序则根据协商的传输协议，发送方执行文件到比特流的转换，接收方逆向实现比特流到文件的转换。

两台电脑之间的交换基本上已经可以实现了。但是互联网的野心不止于此，它想要实现无数台设备的互联！为了实现设备之间的区分，每台设备需要一个 MAC 地址编号，类似门牌号。每两台设备都要相连太过麻烦，不利于拓展，因此互联网定义了集线器、路由器、网关等一系列专职计算机，新设备通过某一个路由器设备，就能连接进由这些机器组件的网络中。网络中的设备组成了一个复杂的图结构，在这张图中搜索一台特定 MAC 的主机并不容易，因此出现了 IP 协议，用子网划分来描述不同的区域，以便路由设备在收到一个未知目的地的通信数据时通过其所属子网判断该将数据转向何处。当今世界，想要能连上互联网的设备数量远超 IPv4 协议的容许上限，目前主流的方案是 NAT 协议，另有 IPv6 协议蓄势待发，这其中涉及到网络安全和历史包袱等多方面考虑。

连接全世界的“官道”（古代政府维护的道路，邮政系统的血管）已经建立，现在你的消息就像货物，可以经过打包（ TCP 、 UDP ）、镖局（ HTTP ）、甚至更安全的 xx山庄（ SSL ），运送至目的地了。除此之外，在这无限广阔的虚拟空间，还有诸如包打听（ DNS ）、各类形形色色的物流服务以及建立在物流服务之上的其他服务粉墨登场，现实世界有多娇，虚拟世界过犹不及。

### *注册一家报社*

倘若你不满足于仅仅做一个互联网的用户，而是也想搭台唱戏，为他人提供自己的服务，你需要有多少准备工作，为此投入些什么呢？

**一个公网 IP 足矣。**只要有这个代表身份地址的令牌，互联网就会对你敞开怀抱，所有与你相关的数据都可以在互联网中驰骋。需要注意的是，域名，一般来说，作为更容易被人记住的 IP 别名，在和公网 IP 具有等价作用的同时，更易被人所接受。

倘若你的目标是建立一个个人博客的服务，从头实现虽非不可，但是工作量实在巨大，有兴致和实力大可尝试，却也无需再看本文了。而目前，简易部署的替代方案很多，这里以[github page](https://pages.github.com/)+[jekyll](https://jekyllrb.com/)为例。

github page 能为你提供一个`$username.gihub.io`域名，通过这个域名，可以不费丝毫代价行使互联网上的所有权利。jekyll 是一个静态网站生成框架，该框架封装了 web 所需的一切细节，你只需要选择一个主题（亦即一个配置），稍加修改，然后专注于自己的创作内容（通常为 markdown 文件），jekyll 会负责将内容转换为 html 网页，并构建一个可以运行的网站系统（简单说，实现一个网站的常见交互功能，例如主页、点击跳转等）。

和 jekyll 相似的框架还有 hexo 、 hugo ，这三者实现不同，但是功能类似。如有兴趣，读者可以自行接触另外两者。

## 今生：本篇博客实现过程

### *1. 选择一个主题模板*

在 Github 上，有许多可供选择的 jekyll 主题，见[jekyll-theme](https://github.com/topics/jekyll-theme)。

大部分的主题 project 中都有详细的说明文档，本博客以[chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)主题为模板，本文也以此为基础详述博客构建过程。

首先，需要将心仪的模板 fock 到个人仓库，并将仓库命名为 `$username.github.io` ，其中 `$username` 为个人昵称，只有如此命名的仓库才能链接到域名`https://$username.gihub.io`。

对于`chirpy`主题来说，作者提供了两个存储仓库，fork 其中任意一个即可，作者更推荐第一个：
- [chirpy-starter](https://github.com/cotes2020/chirpy-starter)
  - 一个模板仓库，将主题的细节隔离到了一个链接的仓库中，保证主题无感升级，不影响使用。
  - 在仓库页面下，点击`Use this template` -> `Create a new repository`，并重命名为 `$username.github.io`。
- [fork-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy/fork)
  - fork 该主题的实例仓库（主题的示例网站的构建源码）。
  - 主题的细节配置都在仓库中，方便自定义开发，但倘若原始主题发生了更新，则此仓库需要自己手动升级。
  - 主题作者建议，除非熟悉 Jekyll 并决心调整或为该项目做出贡献，否则不建议使用此方法。

进入自己刚才 fork 的仓库，点击`setting`，能看到一些设置选项，点击左侧`Pages`，可以进入个人主页的设置界面，这里描述了个人主页的生成细节，包括网站地址、构建和部署方式、自定义域名等等。如无特殊要求，一般保持默认即可，`Custom domain`选项中可以填入自己的域名，即可通过自己的域名访问该界面。

### *2. hello world*

#### >> 环境

顾名思义，github最主要的作用是一个建立在git基础上的开放平台，支持所有用户自由地在远端同步通过git构建的版本库。

对于新手或者非开发人员来说，使用`windows`＋`github桌面程序`是最方便的使用方法，但是本文主要面向有一定编码需求、需要学习常用的编程工具的“新人”（一方面指初学者，另一方面指积极拥抱新事物的人，因为有些资深程序员认为所有可视化都是异端，程序员就该一个终端走天下），因此，本文采用`windows`+`wsl`的方式构建博客使用环境。

笔者目前刚毕业不久，根据笔者7年在校学习经验，认为作为工具和生产环境而言，裸机 linux 并非一个较好的选择。主要原因在于，桌面系统在 linux 中只是一个非必要的组件，其稳定性不佳，有时会陷入崩溃且短时间内很难恢复的状态，例如内核升级、发行版升级、甚至桌面自身的 bug 。诚然，菜是原罪，但是也没必要因此折磨自己。而相反， windows 系统虽有诸多问题，但是其桌面稳定性较高，在 windows 下构建符合个人习惯的办公环境能让这份顺手更持久存在。同时，非桌面化的 linux 系统开箱即用，丢失和恢复成本低，wsl 基于 hyper-V 也具有接近裸机的性能表现，这样的组合能较好实现个人环境调教和 linux 开发习惯的组合。

编辑器选择 VsCode ，它对 windows 自家的 wsl 具有良好支持，插件丰富，拓展性强。终端软件使用 MicroSoft Store 内下载的“终端”，能较为完整地在 windows 平台上实现 linux shell 替代。

笔者接触软件行业的时间不算久，个人习惯也只是刚刚养成，很多地方可能未经时间考验，希望读者多加甄别，按需使用。环境的具体搭建步骤就不详述，鼓励大家思考自己的顺手环境应该是什么样的。不管环境如何，对于构建博客，后续步骤思路都是相通的。

#### >> 拉取仓库到本地

想要对仓库中的内容做修改，直接在网站页面上修改是可行的，但是这不优雅，也不是 git 的常用方式。

git 是一个分布式版本管理工具，分布式意味着 git 可以很方便地在不同机器上同步，版本管理则意味着 git 仓库能够记录文件变更，并支持回退操作。git 常用于多人协同工作，大家使用同一个仓库，在仓库上各自拉取各自的分支，修改后提交分支修改，不会影响他人工作。不同分支可以合并，这样就能将不同人的工作组合起来。大部分情况下，git 仓库会存在一个主要的服务器，其上维护着 master 或者 main 分支，所有成员从该服务器同步仓库并向其提交自己的分支，专门的管理员负责维护 master 分支。需要说明的是，git 本身并没有中心化的约束。git 工具在软件管理中很常用，值得深入学习。

linux 平台中一般预装了 git ，当然，自己装也很简单：

```shell
wsl # 本篇博客基于wsl环境操作

sudo apt-get update && sudo apt-get install -y git # debian/ubuntu系统从apt仓库安装软件
```

然后创建一个自己的工作目录，使用`git clone`命令将仓库从github上拉取下来，其中的仓库地址在仓库主页中点击`Code`获取。

```shell
cd && mkdir workspace && cd workspace

export username="your_user_name" # 填充你自己的github用户名

git clone https://github.com/$username/$username.github.io.git

ls # 输入该指令后，能看到你的仓库文件夹

```

#### >> 调整和改动

如果是拉取的`chirpy-starter`，那么仓库的目录结构应该如下图所示：
![context](/assets/image/20230903/Clip_20230912_214208.png)
_仓库目录结构_

如果是 fork from chirpy ，内容可能会有不同，并且，因为该仓库已经是一个完整的网站实现了，所以下文提到的一些配置或文件，该仓库中可能会有默认值或默认文件存在。

首先关注`_config.yml`文件，该文件需要填入网站的配置信息，特别是个人信息相关的内容。在其中做出一些改动，稍后可以看到对应的效果。

其次，关注`_posts`文件夹，个人所写的博客文档应该放在该目录下，且命名为`YEAR-MONTH-DAY-title.MARKUP`，其中`MARKUP`通常为`md`。尝试写一个 hello world 文件。

```
---
title: hello world
---

Hello World!!!
```
{: file='_posts/2023-09-03-hello_world.md'}

`---`包围住的是文档的元数据，表示文档的一些基本信息，上述示例只配置了标题。在其后所跟着的全部内容组成了文档的正文，由显示字符和标记字符组成，上述示例仅包含一行显示字符，暂未配置任何标记字符。接下来，我们尝试通过 Jekyll 在本地构建静态网站，并预览效果。

#### >> 本地构建查看

一般来说，将修改后的内容直接推到 Github 仓库，所有的修改就能在个人网站主页上查看。但是出于保险，或者对读者的负责，首先在本地检查一下再向外推，是必要的。本地构建需要提供一个构建环境，有两种方案——直接安装和`docker`安装。

- WAY 1

方案一是直接在本地安装构建所依赖的环境。Jekyll 基于`Ruby`语言实现，`RubyGems`提供`Ruby`库和软件包管理功能，类似`pip`之于`python`。

```shell
sudo apt-get install ruby-full build-essential zlib1g-dev

# 如果使用的是zsh终端，就将所有的bashrc改成zshrc
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

gem install jekyll bundler

# 保证在仓库根路径下
bundle install
bundle exec jekyll serve --host=0.0.0.0 --port=4000
```

那么现在，在本地浏览器打开页面`http://localhost:4000/`，就能看到你的个人主页啦！应该类似下面图示，其中侧边内容和你在`_config.yml`文件中配置的内容相关。

![demo](/assets/image/20230903/Clip_20230912_215952.png)
_hello world！_

- WAY 2

方案二是使用`docker`，在隔离的环境中构建和预览。docker 最早定位和虚拟机接近，后来由于云化的发展，docker 逐步转型成为云平台中实现服务部署、环境隔离的最常用方案。docker 非常灵活，其底层支持虚拟化，可成为交叉编译的替代方案，并且在同架构时性能损失很低（网络性能除外）；docker 能够快速部署、释放和重现，对外部环境依赖低，通过 docker 在新机器上部署服务时不用考虑太多环境依赖问题；docker 能将使用的环境封装在容器内，不污染机器外部环境。学习和掌握 docker 的应用几乎成为了程序员的必修课。

官方文档中提供了一个使用 docker 构建的方案：

```shell
# 如果没有将docker加用户组，需要sudo权限运行
docker run -it --rm \
    --volume="$PWD:/srv/jekyll" \
    -p 4000:4000 jekyll/jekyll \
    jekyll serve
```

但是笔者环境中无法运行，大概是由于网络的原因吧。因此笔者给出使用`dockefile`自己制作镜像的替代方案。

首先是安装docker。

```shell
# remove
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
# 网络有问题可以换镜像源
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
"$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# install
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 将用户添加进docker组
sudo groupadd docker
sudo gpasswd -a $USER docker
newgrp docker
```

然后需要自己写一个`Dockerfile`生成要使用的镜像文件。

```Dockerfile
FROM ubuntu:22.04

RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list \
&&  sed -i 's/cn.archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list \
&&  sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list


RUN apt update && apt install -y \
ruby-full build-essential zlib1g-dev

RUN gem install jekyll bundler
RUN gem install \
jekyll-theme-chirpy:6.1.0 \
html-proofer:3.19.4 \
jekyll-archives:2.2.1 \
jekyll-include-cache:0.2.1 \
jekyll-paginate:1.1.0 \
jekyll-redirect-from:0.16.0 \
jekyll-seo-tag:2.8.0 \
jekyll-sitemap:1.4.0 \
nokogiri:1.15.4 \
parallel:1.23.0 \
rainbow:3.1.1 \
typhoeus:1.4.0 \
yell:2.2.2 \
webrick:1.8.1 \
racc:1.7.1 \
ethon:0.16.0 \
rexml:3.2.6 \
google-protobuf:3.24.2

CMD ["jekyll", "--help"]
ENTRYPOINT ["bundle", "exec"]
WORKDIR /srv/jekyll
VOLUME  /srv/jekyll
EXPOSE 4000
```
{: file='~/workspace/jekyll/Dockerfile'}

然后执行:

```shell

cd ~/workspace/jekyll

docker build -t myJekyll .

docker run -it --rm \
    --net host --volume="$PWD:/srv/jekyll" \
    -p 4000:4000 myjekyll \
    jekyll serve

```

可以看到，运行 docker 的指令和官方文档基本一致，只是将镜像替换成了本地构建的镜像。此时使用浏览器打开 wsl 上的4000端口，同样能看到静态网站已经构建。

有关 docker 的更多内容，笔者也会继续学习和整理，敬请期待。

#### >> 提交&&发布

预览没有问题的话，从 git 提交并推到 Github 后，即可在个人的域名上看到自己的博客内容。

首先需要给自己添加一个推送进仓库的权限。Github 目前不支持通过账户密码登录获取推送权限，而是要设置密钥。

在 Github 网站上依次点击个人头像->`setting`->`Developer Setting`->`Personal access tokens`->`Fine-grained tokens`->`Generate new token`，填写名称、时限、描述、仓库范围等字段，并勾选适当的权限（适当的权限全凭自己定义，看不明白的我选择全部勾选），点击生成，此时会出现一段代码，这段代码只会展示一次。

在终端输入

```shell
# ${TOKEN}为生辰的token，${clone_url}为个人仓库克隆链接(不带https://)
git remote set-url origin https://${TOKEN}@${clone_url}

git config --global user.name ${name}
git config --global user.email ${email}

# 以上配置好git环境，以下正式提交

cd ~/workspace/${username}.github.io

git add .

git commit -m '[update] hello world'

git push

```

然后就可以在 Github 仓库页面看到本次提交了，并且个人页面也会在一段时间后刷新。

### *3. 一些细节*

#### >> 文件组织关系

上文中仅仅使用到了`_config.yml`文件和`_post`文件夹，而其他大部分文件都是jekyll框架所需的文件，这里简要描述一下。其中有些文件夹`chirpy-starter`项目中不存在，应该是通过`submodule` 映射到了`assets/lib`目录。

- _config.yml：项目配置文件。
- _data：网站数据文件。Jekyll 将会自动加载此目录下的所有数据文件（后缀为.yml、.yaml、.json、.csv、.tsv），并且可以通过站点变量site.data访问这些文件。
- _drafts：未发布的草稿文件。这些文件的名称不写日期，格式为：title.MARKUP。
- _includes：可被layout布局文件和post博客文件使用的文件，以方便重用。比如导航栏等需要重复使用的源码。
  - footer.html：页脚
  - header.html：页眉
- _layouts：页面模板。
- _posts：博客文档。搭配上post模板便可生成一个个帖子页面。该目录下的文件名应该按照YEAR-MONTH-DAY-title.MARKUP的格式，这样在记录标题的同时，还可以记录帖子的发布时间。
- _sass：用于个性化布局的Sass文件。（Sass 是一个 CSS 扩展语言，后缀有三种，分别是.sass、.scss或.coffee），可以将其处理成单个的 CSS 文件。该目录也可以直接命名为css。
- _site：默认生成的站点。
- .jekyll-cache：保留jekyll serve时生成的页面和标记（例如 Markdown）的副本，以便更快推送服务。
- .jekyll-metadata：该文件帮助 Jekyll 跟踪上次构建之后，哪些文件没有被修改，哪些文件需要在下一次构建中重新生成。
- assets：网站需要的 CSS、JS 和图像文件。
  - css：网站使用的 CSS 文件
  - image：网站使用的图像文件。
- index.html或者index.md：要生成的站点的首页面。

#### >> Github Actions

前文在本地构建步骤中大费周章，那么github又是如何实现自动构建的呢？

该自动构建过程由`.github/workflows`下的文件驱动。github会读取该路径下的`yaml`文件，通过其中的配置自动执行一些检查、构建、编译等操作，在这里称为 Github Actions，其他地方也称CICD。感兴趣的读者可以自行了解。

## 劫：带着脚链跳舞

使用静态网站框架，我们就不需要自己写复杂、冗余的`html`文件了。但是想要实现不同的效果，或多或少的标记必不可少。只能说，便捷和完整永远是一对矛盾体，我们活着的日常就是从各种方面，都尝试让自己处于能接受的范围内罢了。

博客的最后再记录一下markdown常用语法，其实没有什么用，不如一篇`_draft/template.md`来的实在。

```md
---
title: Text and Typography
author: aesalienum
date: 2019-08-08 11:33:00 +0800
categories: [Blogging, Demo] # 类别
tags: [typography] # 标签
toc: true # 右侧目录
comments: false # 评论
pin: true # 置顶
math: true # 数学
mermaid: true # 图表
img_path: "path" # 文中所有图像的父路径
image: # 文档首页图像
  path: /commons/demo.png
  alt: Responsive rendering of Chirpy theme on multiple devices.
---


## Headings

<h1 class="mt-5">H1 - heading</h1>

<h2 data-toc-skip>H2 - heading</h2>

<h3 data-toc-skip>H3 - heading</h3>

<h4 data-toc-skip>H4 - heading</h4>

## Paragraph

正文。

## Lists

### Ordered list

1. Firstly
2. Secondly
3. Thirdly

### Unordered list

- Chapter
  + Section
    * Paragraph

### ToDo list

- [ ] Job
  + [x] Step 1
  + [x] Step 2
  + [ ] Step 3

### Description list

Sun
: the star around which the earth orbits

Moon
: the natural satellite of the earth, visible by reflected light from the sun

## Block Quote

> This line shows the _block quote_.

## Prompts

> An example showing the `tip` type prompt.
{: .prompt-tip }

> An example showing the `info` type prompt.
{: .prompt-info }

> An example showing the `warning` type prompt.
{: .prompt-warning }

> An example showing the `danger` type prompt.
{: .prompt-danger }

## Tables

| Company                      | Contact          | Country |
| :--------------------------- | :--------------- | ------: |
| Alfreds Futterkiste          | Maria Anders     | Germany |
| Island Trading               | Helen Bennett    |      UK |
| Magazzini Alimentari Riuniti | Giovanni Rovelli |   Italy |

## Links

<http://127.0.0.1:4000>

## Footnote

Click the hook will locate the footnote[^footnote], and here is another footnote[^fn-nth-2].

## Inline code

This is an example of `Inline Code`.

## Filepath

Here is the `/path/to/the/file.extend`{: .filepath}.

## Code blocks

### Common

'''
This is a common code snippet, without syntax highlight and line number.
'''

### Specific Language

'''bash
if [ $? -ne 0 ]; then
  echo "The command was not successful.";
  #do the needful / exit
fi;
'''
{: .nolineno }


### Specific filename

'''sass
@import
  "colors/light-typography",
  "colors/dark-typography";
'''
{: file='_sass/jekyll-theme-chirpy.scss' }

## Mathematics

The mathematics powered by [**MathJax**](https://www.mathjax.org/):

$$ \sum_{n=1}^\infty 1/n^2 = \frac{\pi^2}{6} $$

When $a \ne 0$, there are two solutions to $ax^2 + bx + c = 0$ and they are

$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a} $$

## Mermaid SVG

'''mermaid
 gantt
  title  Adding GANTT diagram functionality to mermaid
  apple :a, 2017-07-20, 1w
  banana :crit, b, 2017-07-23, 1d
  cherry :active, c, after b a, 1d
'''

## Images

### Default (with caption)

![Desktop View](/posts/20190808/mockup.png){: width="972" height="589" }
_Full screen width and center alignment_

### Left aligned

![Desktop View](/posts/20190808/mockup.png){: width="972" height="589" .w-75 .normal}

### Float to left

![Desktop View](/posts/20190808/mockup.png){: width="972" height="589" .w-50 .left}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.

### Float to right

![Desktop View](/posts/20190808/mockup.png){: width="972" height="589" .w-50 .right}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.

### Dark/Light mode & Shadow

The image below will toggle dark/light mode based on theme preference, notice it has shadows.

![light mode only](/posts/20190808/devtools-light.png){: .light .w-75 .shadow .rounded-10 w='1212' h='668' }
![dark mode only](/posts/20190808/devtools-dark.png){: .dark .w-75 .shadow .rounded-10 w='1212' h='668' }

## Video

{% include embed/youtube.html id='Balreaj8Yqs' %}

## Reverse Footnote

[^footnote]: The footnote source
[^fn-nth-2]: The 2nd footnote source
```

