---
title: '[Jenkins 9.1][Git]Jenkins CI-初探'
date: 2017-07-28 14:12:18
categories: "部署"
#tags:["Jenkins","简历"]
---
作者：disappearedgod
文章出处：http://blog.csdn.net/disappearedgod/article/details/43406019
时间：2015-2-2

#0.前言

环境：linux（mint or others）
目标：自动构建deb包
任务简述：
项目组需要一个可以自动构建的环境。其主要目的是自动生成一个linux下编译环境的安装包。其中，要维护3个github远端的git库，和一个自己服务器的打包文件。

#1.安装初步

进入官网选择最近版本
1.进入官网，点击“Use Jenkins”
![Use-Jenkins](Use-Jenkins.png)

2.如果你想要安装Jenkins,点击"Installing Jenkins"
![Installing](Installing.png)

3.本文环境是在Linux下，可以用Unix的Ubuntu环境安装，apt-get 或者Debian包。
![UbuntuInstallation](UbuntuInstallation.png)

4.使用命令安装Jenkins
![Installation-Code](Installation-Code.png)

5.如果想要了解（建议你了解）安装做了什么，下面的话说了很多。

1. Jenkins的默认环境安装在了/var/lib/jenkins下面，本文使用了这个默认的文件夹，当然如果为了jenkins新建一个用户名后（这个是必须的，默认会新建），也可以放在/jenkins/home/下面。
2. 部署jenkins的基本环境。包或了添加了jenkins的用户。
Others. Jenkins为了能够跟java无缝对接，jenkins将一些部署的文件放在了war包里面，如果感兴趣可以尝试定制自己的东西。不过Jenkins并不欢迎你这么做。
Tips:我发现其实Jenkins只是用了这几个目录。 /var/lib/jenkins /var/log/jenkins /var/cache/jenkins
![Details](Details.png)



#2.Jenkins部署初步

2.1 重要概念

在部署前，先需要对jenkins进行简要理解。
Jenkins是一个开源的实现持续集成的软件工具。官方网站：http://jenkins-ci.org/。也就是说，我们在项目中借用Jenkins来持续构建软件。就相当于托付别人办事一样，我们只能监督事情的目的，结果，而不需要也不应该参与事情办理打全过程。通过一些初步部署工作发现，Jenkins在CI过程中不会使用系统用户的权限，而是使用用户（你）赋予Jenkins的权限，用Jenkins来代理一切事物。（jenkins读取你在web中的配置，使用它在本地的权限，使用jenkins了解的ssh权限进行与远端的访问）
如果，你在配置中遇到Permission Deny 或者是一些ssh key的问题，是因为你并没有理解上一段的含义。


2.2 jenkins插件

如果你只需要进行java的环境部署的话，恭喜你，系统已经预先安装了java需要的所有插件，这是因为jenkins的前身huston本来就是为了java的部署服务的，这不是本文讨论核心。
对于Git来说，需要几个东西。
Git plugin：这个插件是用来同步git库的。
Multiply SCMs: 多个SCM通道，每一个SCM都可以部署一个Git。用过Git库的人会问道，Git plugins不是可以同步多个库么？为什么要用这个。答案是：尽管Git库可以同步多个git库，但是需要注意的是Git插件提供branch选项尽管可是该多个，但是是针对与每一个库同时检索多个branch，这个不符合我们使用的原则。对于部署来说，只需要调用一个Git库中的一个分支，所以我妈需要这个plugins。

2.3 jenkins的系统设置

如果你打开打是一个已经安装好打jenkins的话，就像下面打图一样，在默认的网址 localhost:8080中打开部署好的jenkins。如果是刚刚安装过的jenkins，则MainView中不会有东西。
本文以Chrome浏览器为例，因为系统预装的是中文，所以jenkins可以识别，并对大部分内容进行了汉化。细心的人可能已经发现了右上角进入登录界面;如果是第一次使用打话没有登录，是以匿名用户身份进行设置的。不过没有关系，因为在权限设置前，匿名账户会获得所有权限的。
![SystemSetting](SystemSetting.png)


点击左侧的”系统管理“
![管理Jenkins](管理Jenkins.png)

点击主界面中的”插件管理“，然后点击”可选插件“ 找到需要打插件，然后点在下方安装按钮。（PS：如果你找不到安装按钮，可以放大缩小浏览器的显示比例，jenkins并没有很好的研究web端界面）
！[可选插件](可选插件.png)


#3. Git 插件初探

首先，由于Jenkins没有默认安装Git插件，需要Git库同步时候，需要手动选择安装git插件。
3.1 Git plugin 安装

3.1.1 未安装前的样子

下图是没有安装git库的时候的样子，发现“源码管理”中没有git，这是由于jenkins没有默认安装git插件的缘故。
![3-1-1Before](3-1-1Before.png)


3.1.2安装git plugin

在“系统管理”->“管理插件”中找到，“可选插件”选项卡。“过滤“（一个名为过滤的搜素窗口）中可以用键盘输入Git来查找。
我们只点git plugin，jenkins会自动为我们添加其依赖的插件如下图。
![3-1-2安装git-plugin](3-1-2安装git-plugin.png)


等待安装，如果勾选下方的”安装完成后重启Jenkins“的话，会jenkins会重启；或者可以在浏览器中输入”localhost:8080/restart“来重启jenkins。
![3-1-2安装中](3-1-2安装中.png)


3.2 安装Multiple SCMs插件

尽管Git插件可以做到同步多个git库，但是只能同步多个git库的同一个分支，如果你的项目恰好需要不同库的不同分支进行同步的话，这就需要Multiple SCMs插件了。
![3.2 安装Multiple SCMs插件](3.2 安装Multiple SCMs插件.png)

等待安装
![3-2等待安装](3-2等待安装.png)

安装后的效果：在源码管理中可以点选”Multiple SCMs“
![3-2-Multiple SCMs][3-2-Multiple SCMs.png]

点击”AddSCM“，点选Git，可以加入多个库。
![3-2AddSCM](3-2AddSCM.png)

3.3GitHub plugin 安装

3.3.1 安装

同样的在可选插件中选择”GitHub plugin“，jenkins同样会帮助你安装其依赖的其他插件。
![3.3GitHub plugin 安装](3.3GitHub plugin 安装.png)

3.3.2安装效果

可以在Build时候选择，”Build when a change is pushed to GitHub“，当GitHub更新的时候，重建。
![3.3.2安装效果](3.3.2安装效果.png)

3.4 新建Git项目

新建一个项目：（点击左栏菜单的新建）
因为是要与Git同步，所以选择的是没有预先设置的项目(构建一个自由风格打软件项目 free-style)。
![3.4新建Git项目](3.4新建Git项目.png)

点击"OK"后看到下图，当然，会发现有些东西并没有向下面这么全（这是因为我安装了很多其他打插件去尝试，比如GerritRepo等，不过这个跟本文关系比较少，这里不做介绍）
![3.4ELSE](3.4ELSE.png)

选择"源码管理"下的"Git"选项

点击Add添加一个私钥，在A处填写你的姓名，将ssh生成的私钥用gedit or vi打开，将内容复制到B处。
![3-4-Add添加一个私钥](3-4-Add添加一个私钥.png)

如果发现，添加后有红色字体，则证明有问题，Jenkins在这里使用了一个简单的clone命令来尝试添加的私钥是否正确。（用ssh链接git库：git@github:yourteam/yourproject/）

这里可以该git的分支，默认分支是origin下的master分支，在界面中可以改为origin下的任意其他分支。

改变源为远端upstream源
![3-4改变源为远端upstream源](3-4改变源为远端upstream源.png)
结果
![3-4Results](3-4Results.png)

3.5 GerritRepo插件

3.5.1 写在开始——需要注意的事情

GerritRepo需要做的内容完全可以在shell中进行，它的存在只不过是用来简练了代码。

#4. Shell & Repo（python）
Jenkins可以执行脚本，只要你装插件，php，python都可以跑。但是，本文发现，Jenkins中执行source语句并不理想，也许是因为Jenkins固定指向的sh是bash，而ubuntu中默认的是dash的原因。本文采取的方法是，将想要执行打语句写入一个脚本中，并在脚本第一行注释出用什么sh执行，来规避可能存在的风险。
1. 本文采用在shell中直接读repo的绝对路径在初始化。
2. 在workspace中编写一个sh，用bash执行。
![Shell&Repo](Shell&Repo.png)

#5. 其他

5.1 build部署

我们可以设置按照某种周期来执行build（与crontab中相似，5个参数代表了：小时，天，月，年，十年）
![5.1 build部署](5.1 build部署.png)



5.2 一个关键的设置

如果你没有设置build的策略很肯能出现这个问题，由于频率过高或者代码一直在更新，很短时间内，build版本号挤压太多。
![5.2KeySetting](5.2KeySetting.png)

而我们想要的结果是这样的，无论多少个build，都保留最近几天打build
![5-2-build](5-2-build.png)

只需要设置一下策略就好
![5-2-设置一下策略](5-2-设置一下策略.png)




