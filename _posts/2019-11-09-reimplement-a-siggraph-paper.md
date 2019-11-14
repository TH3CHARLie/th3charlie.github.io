---
title: 如何复现一篇 SIGGRAPH 论文
tags: [thoughts]
---

[SIGGRAPH](https://www.siggraph.org/)/[SIGGRAPH Asia](https://sa2019.siggraph.org/) 作为计算机图形学领域的顶级会议，每年分别在北美/环太平洋地区举办一次。该会议近年来每年约接收到 300-500 篇投稿，录用率在 20-30% 之间波动，故一年两次加起来录用的文章大概有200余篇。SIGGRAPH 一直代表了图形学科研的最高水平，学届几乎所有里程碑式的发展都可以在该会议的论文中找到。也正因 SIGGRAPH 的文章含金量高，对于浙江大学这样的中国图形学强校来讲，博士毕业的论文要求就限定于 SIGGRAPH/SIGGRAPH Asia（或者 Transaction of Graphic(TOG)，而 TOG 论文一般会在会议上展示），而如果本科生能够作出一篇 SIGGRAPH 一作来，往往能直接敲开四大的大门，例如南开大学的 [Pingchuan Ma](https://pingchuan.ma/) 和 [Yunsheng Tian](https://www.yunshengtian.com/) 就在 SIGGRAPH 2018 上发表了一篇使用强化学习做流体模拟的论文，顺利进入 MIT CSAIL 深造。几乎每个图形学研究者都以做出能够发表在 SIGGRAPH 上的工作为目标，但要想做出这样的工作往往需要非常巧妙且深入的思考和非常扎实的实践。作为一个初入该领域的菜鸟，我自然也有这样的梦想，但自己无论是理论还是实践都有很大的欠缺，即使急于求成但也难以做出这样的工作。

另一方面，从头发表一篇 SIGGRAPH 论文是很难的，要从头复现一篇却并不复杂。举例来说，我非常敬佩的一位清华学长（目前在 MIT CSAIL 读博）[Yuanming Hu](http://taichi.graphics/me/) 在东京大学交换期间，实现了多篇关于渲染、模拟的 SIGGRAPH 论文，最后整合成了 [Taichi](https://github.com/yuanming-hu/taichi/)，是当时第一个图形学界开源的、渲染、模拟相关的基础设施项目，现在已经转变为一个编程语言（见[论文](http://taichi.graphics/wp-content/uploads/2019/09/taichi_lang.pdf)）。再例如，许多学校开设的课程中都会有作业实现简单而有趣的 SIGGRAPH 论文如 GrabCut、Hybrid Images 之类。在过去的一年里，虽然我并没有能够从头做出一篇 SIGGRAPH 论文，但我成功的在两门课上从头复现了两篇 SIGGRAPH 论文：[Video SnapCut: Robust Video Object Cutout Using Localized Classifiers](https://juew.org/projects/SnapCut/snapcut.htm) 与 [Deep High Dynamic Range Imaging of Dynamic Scenes](https://cseweb.ucsd.edu/~viscomp/projects/SIG17HDR/).

## Video-SnapCut

