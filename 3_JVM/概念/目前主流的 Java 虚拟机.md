- [Java SE / Java EE](#java-se--java-ee)
  - [HotSpot VM](#hotspot-vm)
  - [J9 VM](#j9-vm)
  - [Zing VM](#zing-vm)
  - [JRockit](#jrockit)
- [Java SE Embedded](#java-se-embedded)
- [Java ME](#java-me)
  - [CLDC-HI](#cldc-hi)
  - [J9 VM](#j9-vm-1)
- [Android / Android兼容系统](#android--android兼容系统)
- [JavaCard](#javacard)
- [Sun SPOT](#sun-spot)
- [研究性质的JVM](#研究性质的jvm)
  - [Jikes RVM](#jikes-rvm)
  - [Maxine VM](#maxine-vm)
  - [Graal VM](#graal-vm)


要说主流JVM是什么，首先得区分清楚场景。

光谈部署量的话，搞不好现在部署量最多的JVM是Dalvik / ART…虽然Google会告诉大家Dalvik和ART不是“JVM”，但大家都知道骨子里它就是不折不扣的JVM，毫无疑问。它们的设计处处有标注对JVM规范的参考，以保证语义符合JVM规范的要求；那个基于寄存器的字节码设计只是一种实现优化而已。

# Java SE / Java EE

Java EE是以Java SE为基础的。所以并没有“JVM for Java EE”这么一说，只有“JVM for Java SE”，可以用于Java SE与Java EE。

在这个类别下，主流选择有：（按流行程度递减）
* HotSpot VM
* J9 VM
* Zing VM
* JRockit

## HotSpot VM
HotSpot VM是绝对的主流。大家用它的时候很可能就没想过还有别的选择，或者是为了迁就依赖了Oracle/Sun JDK某些具体实现的烂代码而选择用HotSpot VM省点心。

Oracle / Sun JDK、OpenJDK的各种变种（例如IcedTea、Zulu），用的都是相同核心的HotSpot VM。
从Java SE 7开始，HotSpot VM就是Java规范的“参考实现”（RI，Reference Implementation）。把它叫做“标准JVM”完全不为过。

当大家说起“Java性能如何如何”、“Java有多少种GC”、“JVM如何调优”云云，经常默认说的就是特指HotSpot VM。可见其“主流性”。
（其实这不是件好事；具体到JVM实现才可以讨论的问题还是应该指明讨论是基于哪个实现）

JDK8的HotSpot VM已经是以前的HotSpot VM与JRockit VM的合并版，也就是传说中的“HotRockit”，只是产品里名字还是叫HotSpot VM。

这个合并并不是要把JRockit的部分代码插进HotSpot里，而是把前者一些有价值的功能在后者里重新实现一遍。移除PermGen、Java Flight Recorder、jcmd等都属于合并项目的一部分。

不过要留意的是，这里我说的HotSpot VM特指“正常配置”版，而不包括“Zero / Shark”版。Wikipedia那个页面上把后者称为“Zero Port”。用这个版本的人应该相当少，很多时候它的release版都build不成功…

## J9 VM

J9是IBM开发的一个高度模块化的JVM。

在许多平台上，IBM J9 VM都只能跟IBM产品一起使用。这不是技术限制，而是许可证限制。

例如说在Windows上IBM JDK不是免费公开的，而是要跟IBM其它产品一起捆绑发布的；使用IBM Rational、IBM WebSphere的话都有机会用到J9 VM（也可以自己选择配置使用别的Java SE JVM）。

根据许可证，这种捆绑在产品里的J9 VM不应该用于运行别的Java程序…大家有没有自己“偷偷的”拿来跑别的程序IBM也没力气管（咳咳

而在一些IBM的硬件平台上，很少客户是只买硬件不买配套软件的，IBM给一整套解决方案，里面可能就包括了IBM JDK。这样自然而然就用上了J9 VM。

所以J9 VM得算在主流里，虽然很少是大家主动选择的首选。

J9 VM的性能水平大致跟HotSpot VM是一个档次的。有时HotSpot快些，有时J9快些。

不过J9 VM有一些HotSpot VM在JDK8还不支持的功能，最显著的一个就是J9支持AOT编译和更强大的class data sharing。

## Zing VM

我必须把自家的Zing VM放进“主流”里 >_<

Zing VM是一个从Sun HoSpot VM fork出来的一个高性能JVM，可以运行在Linux/x86-64平台上。Azul为它重新写了一套GC，也修改了VM内的许多实现细节，所以从我们自己的角度看，与其说它是HotSpot VM的一个变种，还不如把它看作“一个全新的JVM、只是凑巧与HotSpot VM很像”更合适。

在要求低延迟、快速预热等的场景里，Zing VM都会比HotSpot VM表现更好。

Zing的C4 GC目前可以支持1TB的-Xmx，而且GC暂停时间仍然可以维持在< 10ms的范围里。实际上Zing现在能支持接近2TB的-Xmx，但我们还没能用来测试2TB场景的机器…据说再过一段时间这个测试服务器就要到货了。

Zing的ReadyNow!功能可以利用之前收集到的profile数据，引导JVM在启动后快速达到稳定的高性能水平，减少启动后从解释执行到JIT编译的等待时间。

Zing自带的ZVision / ZVRobot功能可以方便用户监控JVM的运行状态，从找出代码热点到对象分配监控、锁竞争监控等都可以做到。

最关键的是，Zing能让普通没有为GC之类的底层方向调优的Java应用享有低延迟、快速预热、易于监控的功能，为用户节省苦苦调优的精力和时间。这是Zing的主要价值——很多Java应用都可以通过长期努力在应用/框架层面优化来提升性能，但使用Zing的话就可以更多把精力集中在业务方面。

## JRockit

以前Java SE的主流JVM中还有JRockit，跟HotSpot与J9一起并称三大主流JVM。这三家的性能水平基本都在一个水平上，竞争很激烈。
自从Oracle把BEA和Sun都收购了之后，Java SE JVM只能二选一，JRockit就炮灰了。

JRockit最后发布的大版本是R28，只到JDK6；原本在开发中的R29及JDK7的对应功能都没来得及完成项目就被终止了。

主流就说这么几个吧。其它支持Java SE的JVM还有好多，但是难说是主流。

要比惨的话，Apache Harmony里的DRLVM可能算是最惨之一。背后有IBM和Intel的强力支持，本来有望成为F/OSS系的主流JVM，奈何被Sun阴了一把不让它跑JCK使它不能名正言顺的使用Java™说自己是符合规范的JVM；然后Sun自己赶紧开源了自己的JDK，一直拖到硬把Harmony项目逼死了。

# Java SE Embedded

这是Oracle造出来的比较新的概念。硬件发展得很快，现在很多所谓“嵌入式”场景用的机器其实跟普通台式机的配置没差多少，完全足以运行Java SE，侵蚀了以前高端Java ME（例如Java ME CDC Profile）的地盘。

Oracle Java SE Embedded里带的JVM自然还是HotSpot VM，不过是Java SE Embedded定制版：简化了JVM内的某些部件，尽可能在支持完整的Java SE功能的前提下向着减少内存消耗的方向优化；只留下了Client Compiler（C1）而去掉了Server Compiler（C2）；GC以前好像是只留下了Serial GC但后来有没有支持更多GC种类我不太清楚。

IBM在这个领域照样可以用J9 VM应对。

其它还算主流Java SE Embedded JVM的话，可能JamVM可以算进来吧。它是一个小巧的、能支持完全OpenJDK类库和Java SE规范的JVM。

# Java ME

主流有俩：
• CLDC-HI
• J9 VM

## CLDC-HI
Oracle/Sun系的话，现在主流的Java ME JVM只有CLDC HotSpot Implementation（CLDC-HI，或者叫Monty VM）了。很明显这是用于支撑Java ME CLDC Profile的JVM。

以前还有CDC HotSpot Implementation（CDC-HI，或者叫CVM），但Oracle调整了Java ME战略之后这个VM也炮灰了。目前可能只有在Oracle ADF Mobile在iOS上的版本里还活着吧。（没错，iOS上可以跑Java程序的！）
战略调整后的Java ME CLDC都快能赶上以前的Java ME CDC了。夹在Java ME CLDC与Java SE Embedded之间的Java ME CDC自然得炮灰。

Sun以前在Java ME CLDC还有一个KVM，本来应该早就被CLDC-HI替代，但现在可能还有些小厂商在基于KVM定制自己的Java ME方案，毕竟更加简单而且资源消耗更少。

## J9 VM

IBM在Java ME领域对应的仍然是J9 VM。高度模块化不是吹的。

# Android / Android兼容系统

如同开头说的，Android上的Dalvik / ART虽然名字不叫JVM，但骨子里就是不折不扣的JVM。
这俩VM都能支持几乎完整的Java SE功能。跟一般Java SE相比，可能也就ClassLoader、动态生成字节码之类的方案比较坑爹。

Dalvik VM自身也有不少变种就是了。例如说Intel版的Dalvik x86版重写了许多组件，JIT性能比原版Dalvik VM好。

阿里云OS的LemurVM是一个可以兼容Android的Java应用的JVM。

# JavaCard

Oracle/Sun有JCVM。其它支持JavaCard的JVM我还真没怎么留意。会用到这种解决方案的都是政府或者企业用户吧…不是我能接触到的领域。

# Sun SPOT

Sun SPOT上有个Squawk VM，是一个非常小巧、专门为小型嵌入式环境设计的JVM。

# 研究性质的JVM

去找比较新的、跟JVM相关的学术界的研究论文，基本上就下面几个JVM可选：
* Jikes RVM
* Maxine VM
* Graal VM
这仨就算是现在的主流研究性JVM吧。

## Jikes RVM

Jikes RVM是IBM开发的专门用来研究JVM实现技术的项目。曾用名Jalapeño。
它是一个元循环虚拟机（metacircular VM），整个JVM都是用Java实现的。

最有趣的地方是它有一个名为MMTk的框架：Jikes RVM - MMTk，可以很方便的编写新的GC算法插入到Jikes RVM里去，而且已有的实现的选择也很丰富。所以很多研究GC算法的论文会选择基于Jikes RVM/MMTk来做初步实现，不但做起来容易，还可以很方便的与别的算法对比。

## Maxine VM

Maxine VM是Oracle/Sun Labs开发的研究项目。跟Jikes RVM一样是元循环虚拟机。
Maxine VM自从有了C1X/T1X编译器之后，性能似乎就超过了Jikes RVM；有了Graal编译器之后更加压倒Jikes RVM。做JIT编译器研究的话我会选择用Maxine VM。

## Graal VM

Graal VM就是Maxine VM的Graal编译器插在HotSpot VM上。现在Oracle Labs和一些大学做JIT编译器研究都是基于Graal VM来做的，效果出奇的好，性能基本跟HotSpot Server Compiler（C2）持平甚至有所超越。势头正猛。

以前百家争鸣的时候，研究性JVM或配套的JIT编译器那也是多得要命。ORP、OpenJIT、Moxie、Joeq、CACAO、SableVM、VMKit、Sun ExactVM / ResearchVM…随便列几个。
