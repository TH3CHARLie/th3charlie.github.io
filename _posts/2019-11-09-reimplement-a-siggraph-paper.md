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

事实上，最后展示的时候我们发现，我们的选题是相当正统的。同学们的选题中包含了大量遵循“一份作业多次提交”理念的题目，例如：提交《计算摄影学》的 GrabCut、提交《人工智能》的 深度 3D 点云、提交《编译原理》的 Halide 子集。当然也有一些很有意思的选题，例如有一组做了 Flash/Non-flash photography，非常别致。

### 阅读与编程

选完题之后，我和我的队友 [97littleleaf11](https://github.com/97littleleaf11) 所做的第一件事就是开始反复读论文，确保我们理解了论文的每一个步骤。我觉得自己一个人埋头啃论文和与他人一起读然后讨论是个完全不一样的过程，后者可以让学习速度和深度都显著提高，几个人在一起良性的讨论真的可以迸发出思维的火花。之前在《程序设计方法学》读 PyPy 的论文时我就有过这样的感觉。

在觉得读的差不多，对整个系统有了粗略的把握后，我们就一边精读细节一边开始上手写代码。我们确定的工具链是 C++ 加上 OpenCV 和 QT，整个系统的前后端又可以非常好的分离，于是很自然的我去写 OpenCV 的后端，叶子去写 QT 的前端。为了防止图形学时令人崩溃的协作问题再次出现，我们终于走向了正轨，一人维护一个 branch，靠 PR + CI 来整合代码。特意提起这一点看起来非常可笑，但在一个大四了还有很多人不会用 Git 的学院，学会正确的协作开发课程设计的确是一件值得提起的事情。

SIGGRAPH 论文往往是需要从头搭建一个系统的，特别是渲染/模拟/动画相关的文章。这一点从某些角度上保证了论文的工作量，但另一方面又导致每篇文章都高度耦合，都需要重新造轮子，缺乏开源设施，阻碍了整个领域的良性发展。在这次复现中，我们最终从头开发了一个完整的系统，它做到了功能齐全、结构清晰，包括了论文中提到的每一个模块和细节，但同时它也停留在一个课程设计的简单程度，它的 UI 不够炫酷，它的性能也仅是可用级别。总的来讲，是一个很好的锻炼过程，在系统的构建方面，我们领略了 Adobe System 是怎么去思考并构建一个面向用户的系统的，在编程细节上，我们实践了一些之前听过但没有实际用过的软件工程知识。但另一方面，我们复现的这个 video object cutout system 中很难有可以抽象出来并且复用在下一个 video system 里的代码，这也反映了之前提到的 SIGGRAPH 论文往往耦合太高，难以模块化。

### 测试与评估

实现系统/算法只是一篇的论文的第一环节，另一个重要的环节是详尽的测试、评估与比较。我们复现的过程中，有两个主要的性能参考和比较对象，一是原文中报告的图片、数字以及补充资料中的视频，二是以文章为原型的 After Effects 中的 Rotobrush 工具。我们考虑到后者比较特殊，一是因为其是成熟商业产品中的一个工具，无论是运算性能还是用户体验都是经过仔细优化的，二是因为它实际还至少整合了另外一篇关于颜色模型的文章在其中([参考](https://research.adobe.com/project/snapcut/))，因此可能将是一个非常不公平的比较，但我们认为如果我们实现的原型能够在放宽标准的情况下达到接近这个商业工具的性能的话，将是一个非常好的加分点。

在实现各个小模块的时候，我们的主要比较对象是论文，因为其中给出了每个中间环节的输出，这让我们能够定性的分析这一步是否实现正确。而整个系统实现完后，我们主要的比较对象是 Rotobrush，理由很简单，因为论文中用到的视频素材均是收版权费的，而使用本地安装好的 Rotobrush 的话我们可以做 side-by-side 比较。

开始做 side-by-side 之前我们其实近乎绝望，因为作为一个自动分割系统，我们分割的结果很不理想，初始帧很好的分割在几帧的传播后就乱七八糟，需要加大量的用户修正才能得到能接受的最终结果。好在叶子尝试了 Rotobrush 后发现主要问题可能并不是来自于我们的系统，而是我们选择的输入视频片段过于困难，比如大幅度的运动，难以分辨的前景/背景颜色，这导致即使成熟的商业工具也难以自动得到良好的分割结果，需要大量的人为校正。这给了我们不少信心，我们换用了一些简单的例子，发现我们的系统至少在简单的输入情况下能达到和论文/Rotobrush 相近的性能。随着测试的进一步深入（和 deadline 以小时为单位的逼近），我们检查出了一系列 bug，加上了包括 alpha matting 在内的一系列优化最终结果的方案。最终，我们在相近程度的用户介入下达到了和 Rotobrush 相近的分割效果。当然，在运行效率上我们朴素的、串行的实现显然难以比肩经过仔细优化的 Rotobrush，但和论文中同样朴素的实现相比，在考虑到 CPU 主频的提升后，可以说是在同一个水平上的。

我认为详尽的测试是一篇论文博取其评审和读者非常重要的环节，其受重视程度远不比一个 fancy 的 idea 要低。我认为我们在这次复现中系统、详细的测试、评估与比较是我们最后获得高分的重要原因。

### 展示与报告

展示自己复现的论文是一个很有意思的环节，很有一种 mock oral session 的感觉。在看了前一星期几组同学近乎灾难的展示后，我们总结了一下底下的评委老师主要关注的几个点，在自己的展示中注意。

准备展示的第一环节就是制作 slides，这是我非常享受的一个过程，可以把自己做的东西以简要的文字（主要是 bullet list）、图片、表格用简单但又吸引眼球的方式展示出来。更重要的是，自我初中以来，每次在制作展示用的 slides 时，我就已经会在脑海中模拟自己实际展示时的场景，斟酌展示时的词汇，尤其是开始读论文看展示后，会不自主的把别人用过的感觉很地道很高大的表达代入这个脑内模拟的过程，这也是非常享受的一个源头。在这次制作 slides 的过程中，叶子帮了非常大的忙，他和我两路开工，我做 beamer 的排版，他负责一遍又一遍运行程序给我提供必要的展示用图片，甚至，他还花时间制作了一个非常不错的 demo video，一瞬间感觉至少在架势上我们已经具备了投 workshop 的水平了。

当然，虽然我们在凌晨精心准备了展示，但课程上并不能给我们 20 分钟来一步一步从 introduction 讲到 limitation，尤其是当 12 个组挤在同一节课展示的时候，留给我们的时间只有 5 分钟，我们能做的基本就是展示 demo video，展示一下数据，讲解一下不足，不过，我们的结果在标题亮出来的一瞬间就得到了老师明显的反应，不知是老师对这篇文章很熟悉还是觉得这篇文章很有挑战/新意。

作为课程作业，自然要以报告收尾，由于这并非我们的原创工作，只是单纯的复现，因此在报告中我们并没有提出任何新的想法或者见解，更像是工程记录，把我们实现、测试中的每个环节以文字再叙述一遍，唯一能创新的地方就是在结果测试和比较后，能够总结出不少 limitations and future work。

### 总结

Video SnapCut 是我第一次系统的阅读进而复现一篇图形学相关的论文，我非常满意这一次的经历，在各种意义上我都收获颇丰。

有一个小插曲，在实现的过程中，论文中有一处文字描述和图片有疑似矛盾的地方，这直接导致我们在那一处选择了一个其他的实现，这门课结课一个月后，我写了邮件咨询了原作者 Xue Bai 和 Jue Wang，才发现他们已经从 Adobe Research 到 Megevii 去了，两位先肯定了我们的实现，然后针对我们的疑惑给出了非常详尽的解答。是一次非常不错的体验。

最后，这个课程作业可以在[这里](https://github.com/TH3CHARLie/video-snapcut)看到，[demo video](https://drive.google.com/file/d/15Hm9xs9a6zIIr4fRWtT7tIp9Zf4XIwLm/view?usp=sharing) 也在 Google Drive 上。
