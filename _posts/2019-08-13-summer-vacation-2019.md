---
title: 我的暑假
tags: [thoughts]
---

记录 2019 年炎热的暑假
<!--more-->

*07-06*

到实验室报道，确定了毕设的题目

*07-07*

休息，等《轮到你了》更新

*07-08*

读论文

*07-09*

读论文，配环境

*07-10*

划水，意识到 GRE 要完蛋了。给 Jue Wang 写了封邮件

*07-11*

学习使用 OptiX，读了 OptiX 原论文

*07-12*

拿 OptiX 包装一个 path tracer。中午下暴雨全身都湿了，提前回寝室结果没取到电脑。

*07-13*

提到了新电脑，继续写 OptiX。Jue Wang 回了邮件，并没直接回答我的问题，cc 了 Xue Bai

*07-14*

起床过晚差点错过 GRE 考试，考的也当然是白给。中午吃了喜豚，本とんかつ警察觉得不行，不过晚上和吴先生吃了心心念念的 AJIYA ，非常舒服，牛舌一如既往的给力，点了之前的定番牛大肠反而因为烤的不好所以没吃多少，但是发现牛心管这个新宝贝，太给劲了

*07-15*

下午帮着搭了一下 light stage，晚上才发现自己之前公式也推错了，思路整个也有问题，干了一整周的无用功，悲伤

*07-16*

被 hongzhi 怼出翔了。。。不过的确是他的方案能 work，然后他给我看了 hca 去年写的代码，意外的在代码中发现了 hongzhi 2007 年写的一个完整的数学库。。。好狠一男的。。。

*07-17*

终于写完了第一个粗略的实现，我好菜啊。。。不过总算是敢呼吸了嘤嘤嘤

*07-18*

本该是开心摸鱼的一天，上午开心看着 iOS 教程的时候突然刷到京阿尼起火的事情，整个人开始变得不好，下午写了份周报给鸿智看被怼的很惨，于是在压力下快速把几天前一直没搭起来的学长的翔给编译成功了，然后吃饭时候看到火灾的消息愈发的难过了，好久没有这么难受了......晚上也是一边看新闻一边改 BUG，毫无心思，但是除了愤怒、难过和惋惜以外又不能帮上任何的忙。

*07-19*

采样做 temporal-trace 基本上采了一整天的样

*07-20*

本来是想美滋滋的划水的，可是似乎每次我一打开 iOS 的教程的时候鸿智就会来怼我，然后开始改代码，不过还没改完就和 gg 他们约饭去了，负罪感强烈。

*07-21*

在寝室休息，没去实验室改代码也没去网吧打 dota2，感觉第二周飞快就过去了而自己什么也没做。希望下一周能：1. 修好 trace 的 bug 2. 看看 15-462 写 alien 的代码 3. 每天看下 GRE 的单词 4. 把博客迁移一下

*07-22*

早上到实验室后先把前一天晚上重构的代码整了进去，结果莫名其妙发现整个坐标全乱套了，找了半天才找到了问题。然后中午跟鸿智讨论的时候发现的确出来的结果上都莫名其妙有一层蓝色，一开始我还不信，后来重新花了 7 个小时换了张 envmap 重新采了所有数据，果然奇怪的蓝色就没了，无语。

*07-23*

很悠闲的一天，上午和鸿智讨论的边缘问题一下就处理好了，之后就在看 Swift ，又写了点 Rust，背了下单词，以及 GRE 出作文分了，居然真就随便写都有 3.5 呗

*07-24*

碰到了奇怪的不连续性，debug 的时候根本不知道从哪检查起，因为觉得除了浮点运算的误差外，其他每一步都是正确的。于是盯着 debugger 一顿乱调，打了许多文字和图像出来辅助 debug，最后脑洞大开从原位置打光线做检查，居然就查出了这个 embree 带来的光线与几何不相交的问题。晚上花时间又把 Video-SnapCut 过了一遍，回了 Xue 的邮件。

*07-25*

顺着昨天的发现，继续查 embree 的事情，一开始以为是它内部插出来的 normal 不对，结果试了半天 API 一直报错，文档也没写，GitHub 上好像也能这样用，于是赶紧去提了个 issue，结果人家根本就不支持，我晕。然后继续用 ply 调试了半天也没能想到一个好的办法解决边缘采样的准确度和连续性这个矛盾，本来准备在 hit count, hit distance 上再加一个 sampling surface normal 的，但鸿智说要不就顺着这个方向来，于是又开心的回到了两周前开始的地方。晚上花了点时间，把代码整理了一下，把博客稍微迁了一下。

*07-26*

花了一点时间证明鸿智的想法是错的以及我的代码没有写错，于是顺利开始讨论后面的事情，于是接下来就要正式开始做毕设了，感受到了一瞬间剧增的难度。

> -- 常见的科学研究要么是问题驱动，比如“如何解决ImageNet分类问题”；要么是方法驱动，如 “RBM可以用来干什么”。当时的我同时锁死了要解决的问题和用来解决问题的方案，成功的可能性自然不高。

引陈天奇[机器学习科研的十年](https://zhuanlan.zhihu.com/p/74249758)一文中这一段话自勉。

*07-27*

白天随便读了点祖师爷的论文，因为周六所以下午很早就回了寝室，晚上去打了三把 doto，愉快，居然能赢两把，第三把要不是阵容太差其实也能赢。

*07-28*

休息，下午把毕设的论文啃完了，晚上看了时隔了两周的《轮到你了》，我的黑岛啊QAQ...

另外发现了一个《だから私は推しました》，NHK 居然拍这种题材，真的牛逼，代入感太强了，如果黑岛真的没了的话我就接着看这个了。

*08-01*

写了好几天的 iOS，很烦，麻烦的地方根本就不是 iOS，反正在不讲究代码质量的情况下只要做组合拼凑总是能做出个能跑的玩意，真正麻烦的东西是亘古不变的 camera projector 同步。。。感觉来实验室一个月啥也没做出来，挫败感好强啊。

*08-03*

感觉自己变得滑了起来。。。这几天啥也没产出，一直在翻阅文档，什么也没看进去。晚上又去打了3把 doto，风行真的牛逼。

*08-04*

下午和吴凡、gg、氨水又去吃了烤肉，晚上回来看了《所以我推她》的第二集，看哭惹，接着又看了《轮到你了》，黑岛果然没有便当，而且还让我这一个老年人磕起了 CP，我好喜欢娜娜赛说关西话啊。

*08-05*

研究了一天苹果的各种框架：ARKit, Vision, Core Image, 最后觉得都不适合我的场景，于是采取了使用 OpenCV for Swift 的方案，感觉图像处理这一块没什么坑了，接下来又要踩异步的坑了。

*08-08*

迷茫，iOS 这边完全没动力写。不过因为上午实验室停电，正好休息一天，在图书馆借到了 Multiple View Geometry in Computer Vision，争取每天读一点。

*08-09*

继前一天停电后刚回实验室又马上遭遇了即将到来的台风，匆匆回了。

*08-10*

台风正式过杭州的一天，不过其实还好，大风大雨在我起床前都过去了，中午还悠闲的出门吃了饭。下午读了一下午的 Programming Language Pragmatics，晚上五六点说台风眼要过来我还期待了半天，后来发现只过了东站那一块儿，和我们这边没什么关系，白等了。

*08-11*

周日，照常的看了《所以我推她》和《轮到你了》，继续读书。

*08-12 ~ 08-17*

摸鱼，几乎没有干过什么正事。

*08-18*

前往南京浪极限七小时，路上还在高铁上打了 leetcode contest， 见到了南京地主沁狗，蹭了饭，然后花了一个下午和她逛了玩具城，看乐高和高达看的我自己心里也痒痒的，忍了好久才没能剁手，最后居然在扭蛋机面前没能走得动路，扭了七次外加沁狗帮着找店员换了一个总算是扭出来一整列阪急外加城和铁轨，扭一扭发条还能愉快的动起来。晚上回来又照常看了两部剧。