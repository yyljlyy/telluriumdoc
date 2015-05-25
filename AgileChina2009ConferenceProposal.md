# Tellurium: 一个敏捷的Web测试框架 #

Tellurium是一个开源的Web自动测试框架。大多数的Web测试框架如著名的Selenium和Canno WebTest等都是侧重于单个的UI元素，而Tellurium是基于UI模块的。UI模块是由一组复合的UI对象以嵌套的形式组成。 运用UI模块可以使系统自动生成运行时(Runtime)的Locator（定位器?)，这样使得Tellurium可以自动适应UI模块中元素的改变而更具有鲁棒性。其次，UI模块使得Tellurium有很好的表述性(Expressive)，你可以用更有意义的名称来引用UI元素而不是它的Locator。由于Tellurium侧重一个集合的元素，而不是单个元素，这样使得我们可以利用元素之间的关系来帮助我们定位他们在DOM中的位置。

Tellurium Widget增加了UI模块的重用性。在你的测试代码中可以直接引用这个Widget，而不需要重新定义一个个的单个元素。

Tellurium的UI模板（UI templates）可以用来表述动态网页内容，使得Ajax应用程序的测试变得可行和容易。

此外, Tellurium定义了一系列的Domain Specific Language (DSL)来定义UI模块和对元素的操作，这样增加了表达性使得用户很容易编写, 更新,和维护测试代码。

Tellurium是用Groovy和Java实现的，而测试代码可以用Java、Groovy、和纯DSL脚本来写。综上所述, Tellurium是一个敏捷的Web测试框架.

Tellurium是在Selenium的基础上发展而来的，但它的测试理念和Selenium隽然不同，Tellurium不是一个”记录和重播“模式的测试工具，它要求你先定义你要测试的UI再写测试代码，强调UI模块和测试代码的分离，这样使得测试代码的更新和维护非常方便。

总而言之，Tellurium的主要特色如下：

  1. 用抽象UI物件来表述UI元素
  1. 用UI模块达到结构性测试代码并增加重用性
  1. 用DSL来描述, 操作，和测试UI
  1. Composite Locator用UI元素的属性来表述元素
  1. Group Locating利用一个集合的UI元素之间的关系来定位元素
  1. 动态自动生成运行时的Locator
  1. 用UI模板（UI templates）来表述动态网页内容
  1. 支持XPath
  1. 增加jQuery Selector来提高IE的测试速度
  1. 缓存Locator提供测试速度
  1. 方便JavaScript事件的测试
  1. 用tellurium Firefox plugin, Trump来自动生成UI模块
  1. 提供JavaScript  Dojo 和 ExtJS的Widget框架
  1. 支持数据驱动测试
  1. 支持Selenium Grid
  1. 支持JUnit和TestNG
  1. 支持Maven和ant

一年多以前，Tellurium只有一个项目，现在已经发展到包括以下的子项目Tellurium Core, Tellurium Engine, Tellurium Widget Extensions, Tellurium UI Module Firefox Plugin (Trump), Tellurium Maven Archetypes, 和Tellurium Reference Projects. Tellurium也越来越受到人们的关注。 仅每月Tellurium网站的浏览量就超过15,000, 并稳步增长。Tellurium用户也越来越多，其中不乏来自大公司的用户。

Tellurium 0.6.0已经发布， 我们正在研发0.7.0。非常感谢Agile China 2009提供这样一个好的交流机会。我们会具体介绍Tellurium的概念，测试方法。并提供一系列的实例演示。此外，还会透露一些Tellurium 0.7.0的新特性， 如tellurium的新的Engine项目。 并讨论Tellurium的一些中长期计划和工作。比如 behavior-driven test的支持， 和其他流行测试框架如Fitnesse的结合， 以及移植到其他的语言等等。