---
title: 如果你用GitHub，可以这样提高效率
category: 搬砖码农
date: 2016-01-15 19:22:59
tags:
- Nodejs
- Tool Kit
---


一年时间我们从几个人的开发团队发展到十几人的团队，最初的我们只有两个后端加两个前端，而且其中三个还是实习生。
但今时不同往日，现在我们有前端、后端、测试、运维、设计、产品、交互，成功集齐了七个兵种，可以召唤神龙，赐我们一个敏捷的开发团队。
作为一个敏捷性的开发团队，我们要保持快速的迭代速度的同时保证高质量的代码，这是一个艰巨的过程。

### 版本管理 — Git~Hub
>「开始的开始，我们还是孩子」(出自《北京路的日子》)

开始的开始，我们还是用着SVN，代码存储在公司的服务器中，只有一个分支，提交代码无需审核，无需单元测试。因为我们只有几个人......

作为一个有逼格的团队，后来我们用上了Git，并且把代码迁移到Coding中去，然后引入了名为「git flow」的工作流，关于这个工作流可以参考我们老大的文章：[「企业级开发：Gitflow Workflow工作流」](http://www.jianshu.com/p/104fa8b15d1e)

Coding真的是一款很不错的产品，可称为业届良心。

为什么当初没有直接用GitHub，因为私有仓库贵啊......

但，作为一个有逼格的团队，我们是不会满足于此的。Coding自身的服务很不错了，但是相对于GitHub的一些专业的第三方服务，缺少了那么一些竞争力。我们当时想接入持续集成的服务，但是无奈找不到支持Coding的第三方服务，代码Review也没有一个更让我们舒心的体验，我承认我们要求比较高。再加上Coding的几次down机，其中一次是我们准备发布新版本的那一个下午.....

终于有一天，我们老大两眼发光的跟我们说：我们要买GitHub的服务了，申请到资金了。

于是乎，我们浩浩荡荡的迁移代码库。

迁移到Github之后，我们开始接入各方工具，提高我们的工作效率（钱可不能白花啊）。

### 武装GitHub — ZenHub
> [ZenHub](https://www.zenhub.io/)     
> Project management inside GitHub    
> Transform your GitHub issues

正如Zenhub所说，这是一款项目管理工具，嵌入式集成到GitHub中。作为插件，它的UI完全迎合了GitHub，以至于你难以分别出哪些功能是GitHub的，哪些功能是Zenhub的。

如果你用了ZenHub，它会提供给你：

1. **一套任务面板**，给不同的任务提供不同的泳道，直观的展示每个任务的进度。于是乎我抛弃了Teambition。
2. **一套个人的Todo List**，如果有一个issue或者一个PR需要你来处理，但你现在没有空，你可以把它添加到Todo List中，于是乎我又抛弃了Wunderlist。
3. **一套项目图标与统计**，结合着Github自带的项目图表，这个提供更多维度的数据，但是个人感觉意义不太大。
4. **再加一些小彩蛋**，由于它是作为插件形式嵌入到Github中的，所以会在很多地方提供一些这样那样的按钮，等着你去发现了。

### 持续集成 — TravisCI 
> [TravisCI](https://travis-ci.org/)    
> Test and Deploy with Confidence    
> Easily sync your GitHub projects with Travis CI and you’ll be testing your code in minutes!

不知道你有没有好奇Github上的一些明星项目的这样的标签：

[![Build Status](https://travis-ci.org/strongloop/express.svg?branch=master)](https://travis-ci.org/strongloop/express)

这个是Express的构建状态的标签，格式是svg，它会根据Express的构建状态改变。如果Express的某一次提交是没有通过TravisCI构建的，他会变成 build failing。（有心人可以点击这个标签进去看看。）

我们加入TravisCI主要目的是用于自动化的单元测试，每一次Commit与PR，TravisCI都会从Github仓库中拉取代码，然后执行我们写好的单元测试，通过与否会通过状态展示出来。    
合并的人如果看到build failing，就不会合并了，这在一定程度上保证与提高了代码的质量。

TravisCI除了可以用来自动化单元测试，还可以自动化部署，可以设定，通过测试之后，就自动部署上线。

TravisCI对于公开仓库是免费的，但对于私有仓库是要收费的，而且价格也不便宜。    
如果你觉得贵，这里还有一个可以代替的：**[CircleCI](https://circleci.com/)**    
TravisCI可以做的，它都可以做得到，而且会提供一个免费的私有仓库。    

### 代码Review — Reviewable
> [Reviewable](https://reviewable.io/)
> GitHub code reviews done right

如果你觉得Github自带的Review还不够满足的话，可以尝试一下这款产品。
当你提交一个PR之后，在你的PR中，会在你的评论框中嵌入这样的按钮

![](//dn-cnode.qbox.me/FrDLVgDD6alCXnHAfk4XNHJCCnS_)

点击进入Reviewable的页面对应的PR进行review。

并且一个PR没有完成Review之前，是会一直被警告，让你警惕进行合并。

### 代码覆盖率 — Coveralls
> [Coveralls](http://coveralls.io/)    
> We help you deliver code confidently by showing which parts of your code aren’t covered by your test suite. Free for open source repos. Pro accounts for private repos. Instant sign up through GitHub and Bitbucket.

在明星项目Express的Github项目主页中，你可以看到这样的标签：

[![Coverage Status](https://coveralls.io/repos/strongloop/express/badge.svg?branch=master&service=github)](https://coveralls.io/github/strongloop/express?branch=master)

结合着TravisCI的自动化单元测试，加上这个测试覆盖率，提高你对项目的信心。


### 快速进行沟通 — Slack
> [Slack](https://slack.com/)    
> A messaging app for teams who see through the Earth

相信大部分团队都是使用微信进行沟通的，    
再一次，最为一个有逼格的团队，我们使用了Slack进行团队上的沟通，在Slack上，你可以创建频道(Channel)，不同Channel不同的人进行着不同的话题沟通。我们有设计的Channel、产品的Channel、开发的Channel。我最欣赏的是，Slack可以接入很多第三方服务，Github是其中一个。

接入Github之后，项目的每一个Commit、PR之类的动态信息，会被推送到聊天室中。

助你时刻掌控项目动态。

![](//dn-cnode.qbox.me/FjrMtthZ9pX08AWOKbIPlogDKBcK)

Slack接入Githu只是其中的一个小功能而已，更强大的在于Slack本身，利器如何用，待君慢慢体会。  
  
**但**，Slack有一个硬伤：不会科学上网，用不了呀。


### 最后

再一次，作为一个有逼格的团队，我们依然不会满足现状。

