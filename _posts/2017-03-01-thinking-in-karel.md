---
title: Thinking in Karel
tags: [java]
---

![Karel](/images/thinking-in-karel/karel.jpg)

是的，Karel就是这个只有两只脚的小机器人，他生活在一个由街道交织组成的世界里，他会的动作也少的可怜。同时，Karel也是一门教育用的编程语言，由[Richard E. Pattis](https://en.wikipedia.org/wiki/Richard_E._Pattis)发明，关于这门语言的进一步介绍，可以自行wiki，这里给出下文中提到的教科书：*[Karel The Robot Learn Java](https://web.stanford.edu/class/cs106a/resources/karel-the-robot-learns-java.pdf)* 书中也有对这门语言进一步的介绍。

接触到Karel，源于最近在跟的Stanford CS106 Programming Methodology，我看的是这门课使用的语言是Java，但作为一门入门课程，这门课选用Karel作为Java的过渡语言，介绍程序设计的一些基本概念。课程网站上提供了运行Karel所需的包与Stanford Eclipse，WIN10下配置起来相当麻烦，这里就一笔带过。

<!--more-->

Stanford之所以用Karel过渡，大概是因为他麻雀虽小，却五脏俱全，在同学们初次见到他的时候，Karel只会 move(), turnLeft(), putBeeper(), pickBeeper() 四个动作，而在这基础上，却可以让Karel做出相当复杂的动作。在这个过程中，又可以引入函数，抽象，类，继承等等概念，同时Mehran Sahamil教授在授课的途中也会穿插软件工程方面的细节，像代码风格，注释要求。这样一来，即使是没有编程基础的学生，仅仅通过一周对Karel的学习，都可以对编程有了很深的认识并且也养成了良好的习惯。此举确实高明。

<center>教材*Karel The Robot Learn Java*上提供的可用函数与语句</center>

![KarelCommand](/images/thinking-in-karel/KarelCommand.jpg)

这部分结束后便是Assignment 1，这是一个相当有趣也有一定挑战性的作业，要求对Karel编程，解决四个现实问题。Problem 1是要让Karel取到门前的报纸并返回，送分题，主要是为了训练在写注释时的习惯。Problem 2是要打扫街道，主要考察的是循环，也没难度。

Problem 3有点难度了，要求Karel布置棋盘，并且要能够适配任意形状的棋盘，难度出在哪呢？虽然在for语句中使用了计数变量i，但此时课程上还并未介绍变量的定义与赋值，也没介绍判断语句，换句话说就是不能用计数的方式来完成交替放置Beeper的动作。

![Problem3](/images/thinking-in-karel/KarelProblem3.jpg)

那么怎么办呢？注意到可用库中有一个 beepersPresent() 函数，通过对当前方块状态的判断，便可以让Karel做出对应的行动。仔细想想这样一个理念，和图灵机的原理神似，都是通过读取当前方格上的数据进行下一步操作。这个Karel，不可小视。

Problem 4也有难度，要求Karel找到第一行的中点并放置一个Beeper，然而，呆萌的Karel不会数数，更不会跳出他所在的维度一眼看到自己生活在一个几行几列的世界里。

![Problem4](/images/thinking-in-karel/KarelProblem4.jpg){: .center-image }

Mehran Sahamil在授课时和同学们一起解决了一个问题，让不会数数的Karel将一个方块上的Beeper数加倍，用的是“捡一个，放两个”的思路。类似的，这道题我是通过“两侧压缩”的思路。先在每个点上都放置Beeper，每次转向前捡起一个，再回头抵达第一个没有Beeper的位置，直至最后停在中点。现在描述起来确实容易，但当时做的时候要忘记自己所学过的其他知识，转变思维方式，用提供的这些工具，完成这样一个问题，确实挺锻炼脑子的。

完成Assignment之后，又想起课上教授在解决了加倍问题之后说过的“Karel已经可以解决任何数学问题了”，结合我在Problem 3后关于图灵机的联想，不禁让我感到好奇。便Google了一发，果真如此：

- **Karel在第一行第一列这样一个"L"字形区域活动时，可以看作图灵机中无限长的纸带。**
- **Karel的世界里，方块上有2个Beeper可以代表图灵机中的0，1个Beeper代表1，没有Beeper代表空方格。**
- **图灵机的五个动作（左移一格，右移一格，打印数字0，打印数字1，擦去数字），每一个动作都可以在Karel中由对应的方法实现。**


这样一来，Karel这样一个不起眼的小机器人，却有了和图灵机一样强大的计算能力，不禁令人心生敬意。

最后说些题外话，CS106A，找不到合适的形容词来形容这门课的优秀，尤其是Mehran Sahamil 教授，授课幽默风趣，却又能在欢声笑语之中将编程的核心与细节传授与人，关于软件工程的讲述更是相当深刻，私以为这才是 CS 课堂该有的样子，这才是 CS 讲师的标杆。虽然Mehran Sahamil 教授已不再担任 CS106A 的授课，但他还会继续影响像我一样来自世界其他地方的学生。无论是网易公开课，还是Youtube下的留言，皆是赞美与感谢。反观我校 CS 的教学，只得长叹一声，唉......

{: .center-image }