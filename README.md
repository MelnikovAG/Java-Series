![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)
![](https://parg.co/bDm)

![](https://i.postimg.cc/NMZrqkFd/image.png)

# Java 开发基础与工程实践

Java 是由 Sun Microsystems 公司于 1995 年 5 月推出的高级程序设计语言，Java 当初诞生的时候，正是上世纪 90 年代末互联网兴起的时代，在企业应用开发中存在几个问题，一是以 IBM，SUN 和 HP 的 UNIX 服务器和大型机为主的异构环境，C/C++ 和其它语言编写的应用跨平台支持和移植比较困难，二是基于 CGI 和其它技术的网络应用从开发效率和功能性角度来看都不够理想，三是 C/C++在当时是主流编程语言，门槛高、易出错、对经验要求很高，而 Java 简单易学、安全可靠，并且一次编写到处运行，再加上 Applet、Servlet 和 JSP 技术，解决了这些痛点，满足了当时互联网程序设计和运维的要求，伴随着互联网的发展一下子就脱颖而出并长期占据主流地位。

任何一种编程语言如果要获得用户和开发者的认可，一定是要解决一些应用开发和运维的痛点的。Java 能够长盛不衰得益于在标准的统一和开放基础上不断的与时俱进。Java 除了是一种编程语言，也同时是一个运行时，为了能够在最广泛的平台和环境中运行，在诞生伊始就联合各个厂商和组织形成语言和虚拟机统一标准，并通过 TCK 对标准的具体实现进行认证，保障了来自于任何一个厂商的 JDK 的兼容性，使得 Java 没有出现如 UNIX 系统那样的问题。开放性是 Java 生命常青的另一个基石，Java 的演进一直由各个厂商和用户组成的社区来协调和驱动，遵从 JCP 的流程来讨论决定重大特性和问题，这一点保障了 Java 生态的发展壮大和活跃。社区和生态的活跃反过来又促进了 Java 的发展，Java 的一些特性和类库就是直接继承自社区的项目，比如 JDK 5 引入的 JSR 166 until.concurrent，JDK 8 引入的新 Java date 和 time API 等等。正在开发中的很多重要项目，比如 Amber、Valhalla、Loom 等等，也都是社区呼声很高的，并且在迭代中积极吸纳社区的意见和反馈。

![Java Platform Standard Edition](http://static.oschina.net/uploads/space/2015/0917/192918_c6O7_1434710.png)

# 版本

2018 年 3 月 21 日，Oracle 官方宣布 Java 10 正式发布。这是 Java 大版本周期变化后的第一个正式发布版本。需要注意的是 Java 9 和 Java 10 都不是 LTS 版本。和过去的 Java 大版本升级不同，这两个只有半年左右的开发和维护期。而未来的 Java 11，也就是 18.9 LTS，才是 Java 8 之后第一个 LTS 版本。

```sh
/jdk-10/bin$ ./java -version

openjdk version "10" 2018-03-20

OpenJDK Runtime Environment 18.3 (build 10+46)

OpenJDK 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

这种发布模式已经得到了广泛应用，一个成功的例子就是 Ubuntu Linux 操作系统，在偶数年 4 月的发行版本为 LTS，会有很长时间的支持。如 2014 年 4 月份发布的 14.04 LTS，Canonical 公司和社区支持到 2019 年。类似的，Node.js，Linux kernel，Firefox 也采用类似的发布方式。

从 JDK 10 开始，Java 改为每 6 个月发布一个 feature release，这是 Java 适应云时代技术快速发展的另一个重要举措。过去一个主版本的发布需要 3 年甚至更久，使得很多社区期待但又不用很长时间就可以实现的特性，也不得不等待更久。6 个月一个版本，使得短平快的特性能够及时发布来满足开发者需求，同时对于大的复杂的特性又能够分解成小的可以发布的单元，及时获得社区的使用和反馈。在 6 个月一个版本的基础上，针对需要更加稳定的运行环境的企业客户，我们又提供 LTS 版本。这个新的 release 模式，使得 Java 能够同时满足两种不同类别用户的需求，更加有利于 Java 向前推进。

# About

## Copyright & More | 延伸阅读

笔者所有文章遵循 [知识共享 署名-非商业性使用-禁止演绎 4.0 国际许可协议](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)，欢迎转载，尊重版权。如果觉得本系列对你有所帮助，欢迎给我家布丁买点狗粮(支付宝扫码)~

[![技术视野](https://s2.ax1x.com/2019/12/03/QQJLvt.png)](https://github.com/wx-chevalier/Awesome-MindMaps)

您还可以前往 [NGTE Books](https://ng-tech.icu/books/) 主页浏览包含知识体系、编程语言、软件工程、模式与架构、Web 与大前端、服务端开发实践与工程架构、分布式基础架构、人工智能与深度学习、产品运营与创业等多类目的书籍列表：

[![NGTE Books](https://s2.ax1x.com/2020/01/18/19uXtI.png)](https://ng-tech.icu/books/)
