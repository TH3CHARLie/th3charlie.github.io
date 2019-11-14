---
title: 如何复现一篇 SIGGRAPH 论文
tags: [thoughts]
---

[SIGGRAPH](https://www.siggraph.org/)/[SIGGRAPH Asia](https://sa2019.siggraph.org/) 作为计算机图形学领域的顶级会议，每年分别在北美/环太平洋地区举办一次。该会议近年来每年约接收到 300-500 篇投稿，录用率在 20-30% 之间波动，故一年两次加起来录用的文章大概有200余篇。SIGGRAPH 一直代表了图形学科研的最高水平，学届几乎所有里程碑式的发展都可以在该会议的论文中找到。

<!--more-->

也正因 SIGGRAPH 的文章含金量高，对于浙江大学这样的中国图形学强校来讲，博士毕业的论文要求就限定于 SIGGRAPH/SIGGRAPH Asia（或者 Transaction of Graphic(TOG)，而 TOG 论文一般会在会议上展示），而如果本科生能够作出一篇 SIGGRAPH 一作来，往往能直接敲开四大的大门，例如南开大学的 [Pingchuan Ma](https://pingchuan.ma/) 和 [Yunsheng Tian](https://www.yunshengtian.com/) 就在 SIGGRAPH 2018 上发表了一篇使用强化学习做流体模拟的论文，顺利进入 MIT CSAIL 深造。

几乎每个图形学研究者都以做出能够发表在 SIGGRAPH 上的工作为目标，但要想做出这样的工作往往需要非常巧妙且深入的思考和非常扎实的实践。作为一个初入该领域的菜鸟，我自然也有这样的梦想，但自己无论是理论还是实践都有很大的欠缺，即使急于求成但也难以做出这样的工作。

另一方面，从头发表一篇 SIGGRAPH 论文是很难的，要从头复现一篇却并不复杂。举例来说，我非常敬佩的一位清华学长（目前在 MIT CSAIL 读博）[Yuanming Hu](http://taichi.graphics/me/) 在东京大学交换期间，实现了多篇关于渲染、模拟的 SIGGRAPH 论文，最后整合成了 [Taichi](https://github.com/yuanming-hu/taichi/)，是当时第一个图形学界开源的、渲染、模拟相关的基础设施项目，现在已经转变为一个编程语言（见[论文](http://taichi.graphics/wp-content/uploads/2019/09/taichi_lang.pdf)）。再例如，许多学校开设的课程中都会有作业实现简单而有趣的 SIGGRAPH 论文如 GrabCut、Hybrid Images 之类。在过去的一年里，虽然我并没有能够从头做出一篇 SIGGRAPH 论文，但我成功的在两门课上从头复现了两篇 SIGGRAPH 论文：[Video SnapCut: Robust Video Object Cutout Using Localized Classifiers](https://juew.org/projects/SnapCut/snapcut.htm) 与 [Deep High Dynamic Range Imaging of Dynamic Scenes](https://cseweb.ucsd.edu/~viscomp/projects/SIG17HDR/).

## Video SnapCut

在大三上学期，我选了周昆老师主讲，其他 CAD & CG 实验室老师辅助的《计算机图形学研究进展》课程。这门课每周会选取一个主题，分享四到五篇与其相关的论文。前半学期所选取的主题基本上都是周老师自己一路走来做过的题目，所以讲起来非常生动（吹起牛皮来也非常的有底气）。与之相关的，既然授课内容就是读论文分享论文为主，这门课的课程作业自然也就是选择一篇 SIGGRAPH 论文进行复现。

### 选题

首先要经历的是选题，我在选题上主要考虑的是三点：
1. 创新度：这篇论文是否足够有新意，还是只是刷分的 incremental work
2. 难度：在有限的时间里复现这篇论文是否有难以逾越的障碍，如：工程量巨大（5w 行以上代码量）、缺少数据
3. 相关度：选择的论文是否与课程内容相关

最后我定下来的题目是来自 University of Minnesota 和 Adobe System 的 Video SnapCut，按照我的三个标准来看，这篇文章相应做到了：

1. 这是一个 automatic video object cutout system，在论文发表的当时足够创新，且与之前尝试过解决这个问题的工作相比，这篇文章用了 local + global 的策略来提高系统的性能，这一点也相当有创新性。
2. 这篇文章并没有任何的开源代码或者相关资源，唯一的教程就是论文本身，复现起来还是非常有挑战的。但同时，我粗略读过之后觉得并没有特别困难的地方，也迅速确定了相应的工具链。因此我认为，作为一个课程作业，复现这篇论文的难度是非常合适的。
3. 我们在课程中花了一个专题来讲 image editing，尤其是 GraphCut, GrabCut, Lazy Snapping 这些经典而巧妙的方法, 而 Video SnapCut 很有将这些方法从图片推广到视频的风味，因此和课程本身也是非常相关的。

事实上，最后展示的时候我们发现，我们的选题是相当正统的。同学们的选题中包含了大量遵循“一份作业多次提交”理念的题目，例如：提交《计算摄影学》的 GrabCut、提交《人工智能》的 深度 3D 点云、提交《编译原理》的 Halide 子集。当然也有一些很有意思的选题，例如有一组做了 Flash/Non-flash photography，很有意思。

### 阅读与编程

选完题之后，我和我的队友 [97littleleaf11](https://github.com/97littleleaf11) 所做的