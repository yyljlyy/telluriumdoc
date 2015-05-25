

# 简介 #

## 挑战和问题 ##

随着富Internet应用（RIA）和基于Ajax的web应用的兴起，对它们的自动化测试一直是软件测试领域中最热门也最重要的话题。而随着像RIA和Ajax这样新技术的出现，Web自动化测试工具必须能跟上这些技术的变化，并能够解决下面这些挑战：

  * **JavaScript事件**：JavaScript是现在Web应用的统治者。而许多Web应用更是JavaScript的重度采用者。为了测试JavaScript，自动化测试框架应该能够以非常方便的方式来触发JavaScript事件。

  * **服务于动态Web内容的Ajax**: Web应用具有很多桌面应用不具备的优势；比如它们没有安装过程，更新升级是瞬时的而且更容易支持。Ajax是一种非常便利的方式来更新web页面的一部分，而不用刷新整个页面。AJAX为web应用带来更加丰富的功能和用户友好的界面。Ajax应用的web内容通常是动态的，比如，在数据表格中，行数和行内的数据在运行时动态发生变化。

  * **智能/快速响应变化**：一个好的自动化web测试工具应该能在某种程度上解决这个web内容动态变化的问题，才不会让用户不停地更新维护测试代码。

  * **易于维护**：在敏捷开发中，软件开发是基于迭代进行的，新的功能会在每个sprint中添加进来。功能测试或者用户验收测试必须针对新的特性进行及时地重构和更新。测试框架应该能为用户提供一定的灵活性来便于维护测试代码。

  * **重用性**：许多web应用会对应用的不同部分使用完全一样的UI模块。而采用Dojo和ExtJS这样的JavaScript框架则加剧了为不同的web应用使用相同的UI模块的可能。一个好的测试框架应该能提供对这些模块测试的可重用性。

  * **丰富的表现力**：测试框架应该为那些不具备太多编码经验的用户提供以熟悉的方式轻松编写代码或脚本的能力，比如使用某种领域特定语言（DSL）。

## 已有的开源Web自动化测试框架 ##

Selenium web测试框架是现在最流行的开源自动化测试框架。它是一款具有开拓意义的测试框架，提供了许多独具风格的特性和优势，比如基于浏览器的测试、Selenium Grid以及通过Selenium IDE进行“录制和回放”的用户体验。它的一个主要缺点在于它侧重于某个单独的UI元素的交互，比如链接和按钮。这样的话，Selenium就很难解决动态web内容的问题。“录制和回放”对于用户来说的确很简单，尤其是非开发者创建测试案例时。然而，它同样带来难于重构和维护的缺陷。其他的负面包括UI元素和测试代码耦合太紧，、句法冗长、代码脆弱而且对web内容动态变化没有响应。

以下面的Selenium测试代码为例：

```
   setUp("http://www.google.com/", "*chrome");
   selenium.open("/");
   selenium.type("q", "Selenium test");
   selenium.click("//input[@value='Google Search' and @type='button']");
```

你知道上面的代码是在测试什么UI模块吗？这里的locator“q”是什么意思？如果XPath“//input[@value='Google Search' and @type='button']”因为web内容的变化失效了怎么办？很有可能的是，你不得不浏览整个测试代码才能定位你需要更新的代码部分。如果你的代码里面有几十甚至上百个locator怎么办？使用Selenium IDE来创建测试代码是比较容易的，但很难泛化并重构。因为硬编码locator的使用，以及locator和测试代码之间的耦合，是的重构相比较从头开始创建新的测试代码来说，是更加单调乏味和痛苦的过程。维护代码是件麻烦事，因为测试代码不是结构化的。因为Selenium只是侧重在单个的UI元素上，所以就很难调整代码来适应动态web上下文的变化。

Canoo WebTest是另外一个流行的开源Web自动化测试工具。同样，它也是基于单个的UI元素，因此遭遇到和Selenium类似的那些缺陷，比如测试代码和UI耦合很紧。下面是测试代码的示例：

```
    webtest("check that WebTest is Google's top 'WebTest' result"){
       invoke "http://www.google.com", description: "Go to Google"
       verifyTitle "Google"
       setInputField name: "q", value: "WebTest"
       clickButton "I'm Feeling Lucky"
       verifyTitle "Canoo WebTest"
    }
```

Twill是一个简单的脚本语言，允许用户从命令行接口浏览Web。它使用链接、表单、cookies和大多数标准的Web特性。它同样能够处理单个的UI元素，但不支持javascript，而且HTML需要以交互式进行解析。Twill只支持Python，这对一些实际的web应用来说相当不方便。

## Web测试的新方法 ##

### 动机 ###

既然已有的众多自动化测试框架无法有效地解决我们的挑战和问题，我们就有动机需要一种全新的自动化测试框架来解决它们：

  * 智能/响应变化；可以定位变化
  * 解决动态web上下文变化，比如JavaScript事件和Ajax
  * 易于重构和维护
  * 模块化；测试模块可重用
  * 表达力强，易于使用

我们的解决方案就是Tellurium自动化测试框架，简称Tellurium。

### 为什么Tellurium是Web测试的新方法 ###

Tellurium自动化测试框架是一款针对web应用并解决了web测试中挑战和难题的开源自动化测试框架。

大多数已有的web测试工具/框架都侧重于单个的UI元素，比如链接和按钮。Tellurium引入了“UI模块”的概念来实现一种全新的Web自动化测试方法。UI模块是一组UI元素的集合。通常，UI模块代表了以基本UI元素相嵌套的形式存在的一个符合UI对象。例如，Google搜索UI模块可以表示成下面这样：

```
ui.Container(uid: "GoogleSearchModule", clocator: [tag: "td"], group: "true"){
   InputBox(uid: "Input", clocator: [title: "Google Search"])
   SubmitButton(uid: "Search", clocator: [name: "btnG", value: "Google Search"])
   SubmitButton(uid: "ImFeelingLucky", clocator: [value: "I'm Feeling Lucky"])
}
```

Tellurium是在UI模块的基础上构建的。UI模块的存在使得在运行时为UI元素构建locator成为可能。首先，这会让Tellurium对来自内部UI元素的变化更显智能和及时响应。第二，UI模块使得Tellurium具有丰富的表达能力。只需要简单地为特定元素的路径附加名称（uid）就可以引用到UI元素。这样也使得Tellurium的“组定位”特性，重用复合对象以及解决动态web页面变化成为可能。

Tellurium是用Groovy和Java实现的。测试案例可以使用Java、Groovy或者纯的“领域特定语言”脚本来编写。Tellurium从Selenium进化而来，但UI测试方法是完全不同的。比如说，Tellurium并不是一个“录制回放”风格的框架，它要求UI模块从测试代码中分离开来，使得重构变得容易。例如，一旦你定义了上面的Google搜索的UI模块，你就可以像下面这样编写自己的测试代码：

```
type "GoogleSearchModule.Input", "Tellurium test"
click "GoogleSearchModule.Search"
```

Tellurium在运行时自动完成“对象到Locator的映射（OLM）”，这样UI对象就可以简单地通过“复合Locator”由自己的属性来进行定义。Tellurium使用“组定位概念（GLC）”来发现UI组件集合中的信息，locator从而发现它们的元素。Tellurium还定义了一套DSL来用于web测试。更进一步，Tellurium使用UI模板在运行时定义成套的动态UI元素。因此，Tellurium是非常智能的，具有丰富的表达能力，灵活可重用并易于维护。

Tellurium的主要特性包括：

  * 抽象UI对象封装webUI元素
  * 用于结构和测试代码的UI模块以及可重用性
  * 可用于UI定义、动作和测试的DSL
  * 使用一套属性来描述UI元素的复合Locator
  * 使用组定位来发现UI组件集合中的信息
  * 动态生成运行时locator来定位变化
  * 针对动态web内容的UI模板
  * 支持XPath
  * 支持jQuery selector来提高在IE中的测试速度
  * 缓存Locator来提升速度
  * 支持Javascript事件
  * 使用Tellurium的Firefox插件Trump来自动生成UI模块
  * 支持Dojo和ExtJS widget扩展
  * 支持数据驱动测试
  * 支持Selenium Grid
  * 支持JUnit和TestNG
  * 支持Ant和Maven

### Tellurium是如何解决挑战和问题的？ ###

首先，Tellurium没有使用“录制和回放”。相反，它使用Tellurium的Firefox插件Trump来为你生成UI模块（不是测试代码）。然后你就可以根据这些UI模块来创建自己的测试代码。就这样，UI和测试代码就解耦了。Tellurium的结构化测试代码使得重构和维护代码变得容易得多。

复合locator使用UI元素属性来定义UI，而真正的locator（比如xpath或者jQuery selector）会在运行时才生成出来。任何针对复合locator的更新都会导致不同的运行时locator，这样就能定位UI模块内部的变化。组定位则用于去除UI对象对外部Ui元素的依赖（比如大多数case下外部UI变化不会影响到当前UI模块），这样你的测试代码针对变化就能达到一定程度的智能和响应能力。

Tellurium在UI对象中使用“respond”属性来指定JavaScript事件，余下的事情就交给框架自己来自动处理了。UI模板是Tellurium的一个强大特性，能在运行时代表许多相同的UI元素或者动态大小的不同UI元素，这在测试动态web上下文比如数据表格时相当有用。“Option”UI对象设计用来通过多个可能的UI模式来自动解决动态web上下文。

在一个ewb应用中使用UI模块，在多个不同的web应用之间借助Tellurium Widget就可以实现可重用性。而通过Tellurium的领域特定语言（DSL），你可以使用非常具备表达力的方式来定义UI模块，并编写测试代码。Tellurium还为你提供了使用Java、Groovy或者纯DSL脚本来编写测试代码的灵活性。

在接下来的部分，我们会简要地介绍Tellurium，包括它的架构和主要概念。然后会介绍Tellurium的子项目以及如何获取并使用Tellurium。接着是一个详细的关于如何创建Tellurium测试案例的指南（代码示例）。最后，我们会进行总结并公布未来的计划。

# Tellurium Overview #

## Tellurium Architecture ##

The Tellurium framework architecture is shown as in Figure 1.

http://tellurium-users.googlegroups.com/web/TelluriumUMLSystemDiagram.png?gda=0H5Erk8AAAD5mhXrH3CK0rVx4StVj0LYqZdbCnRI6ajcTiPCMsvamYLLytm0aso8Q_xG6LhygcwA_EMlVsbw_MCr_P40NSWJnHMhSp_qzSgvndaTPyHVdA&gsc=cq8RLwsAAADwIKTw30t0VbWQq6vz0Jcq

Figure 1. Tellurium Architecture.

The DSL parser consists of the DSL Object Parser, Object Builders, and the Object Registry.

Thanks to Groovy’s builder pattern, we can define UI objects expressively and in a nested fashion. The DSL object parser will parse the DSL object definition recursively and use object builders to build the objects on the fly. An object builder registry is designed to hold all predefined UI object builders in the Tellurium framework, and the DSL object parser will look at the builder registry to find the appropriate builders. Since the registry is a hash map, you can override a builder with a new one using the same UI name. Users can also add their customer builders into the builder registry.

The DSL object definition always comes first with a container type object. An object registry (a hash map) is used to store all top level UI Objects. As a result, for each DSL object definition, the top object ids must be unique in the DslContext. The object registry will be used by the framework to search objects by their ids and fetch objects for different actions.

The Object Locator Mapping (OLM) is the core of the Tellurium framework and it includes UI ID mapping, XPath builder, jQuery selector builder, and Group Locating.

The UI ID supports nested objects. For example, "menu.wiki" stands for a URL Link "wiki" inside a container called "menu". The UI ID also supports one-dimensional and two-dimensional indices for table and list. For example, `"main.table[2][3]"` stands for the UI object of the 2nd row and the 3rd column of a table inside the container "main".

XPath builder can build the XPath from the composite locator, i.e., a set of attributes. Starting with version 0.6.0, Tellurium supports jQuery selectors to address the problem of poor performance of XPath in Internet Explorer. jQuery selector builders are used to automatically generate jQuery selectors instead of XPath with the following advantages:
  * Faster performance in IE.
  * Leverage the power of jQuery to retrieve bulk data from the web by testing with one method call.
  * New features provided by jQuery attribute selectors.

The Group Locating Concept (GLC) exploits the group information inside a collection of UI objects to help us find the locator of the collection of UI objects.

The Eventhandler will handle all events like "click", "type", "select", and so on. The Data Accessor is used to fetch data or UI status from the DOM. The dispatcher will delegate all calls it receives from the Eventhandler and the data accessor to the connector, which connects to the Tellurium engine. The dispatcher is designed to decouple the rest of the Tellurium framework from the base test driving engine so that we can switch to a different test driving engine by simply changing the dispatcher logic.

## Tellurium UI Objects ##

Tellurium provides a set of predefined UI objects, which users can use directly.

The basic UI object is an abstract class. Users cannot instantiate it directly. The basic UI Object works as the base class for all UI objects and it includes the following attributes:

  1. uid: UI object's identifier
  1. namespace: for future extension
  1. locator: the locator of the UI object, could be a base locator or a composite locator
  1. respond: the JavaScript events the UI object can respond to. The value is a list.

All UI Objects will inherit the above attributes and methods. But be aware that you usually do not call these methods directly and you should use DSL syntax instead. For example, use:

```
click "GoogleSearchModule.Search"
```

In this way, Tellurium will first map the UIID "GoogleSearchModule.Search" to the actual UI object and then call the _click_ method on it. If that Ui object does not have the _click_ method defined, you will get an error.

The predefined Tellurium UI objects and some of their default attributes are listed in the following table:

| **Tellurium Object** | **Locator Default Attributes** | **Extra Attributes** | **UI Template** |
|:---------------------|:-------------------------------|:---------------------|:----------------|
| Button               | tag: "input"                   |                      | no              |
| Container            |                                | group                | no              |
| CheckBox             | tag: "input", type: "checkbox" |                      | no              |
| Div                  | tag: "div"                     |                      | no              |
| Form                 | tag: "form"                    | group                | no              |
| Image                | tag: "img"                     |                      | no              |
| InputBox             | tag: "input"                   |                      | no              |
| RadioButton          | tag: "input", type: "radio"    |                      | no              |
| Selector             | tag: "select"                  |                      | no              |
| Span                 | tag: "span"                    |                      | no              |
| SubmitButton         | tag: "input", type: "submit"   |                      | no              |
| UrlLink              | tag: "a"                       |                      | no              |
| List                 |                                | separator            | yes             |
| Table                | tag: "table"                   | group, header        | yes             |
| StandardTable        | tag: "table"                   | group, header, footer | yes             |
| Frame                |                                | group, id, name, title | no              |
| Window               |                                | group, id, name, title | no              |



## UI Module ##

The UI Module is the heart of Tellurium. The UI module is a collection of UI elements grouped together. Usually, the UI module represents a composite UI object in the format of nested basic UI elements. For example, the download search module in Tellurium's project site is defined as follows:

```
ui.Form(uid: "downloadSearch", clocator: [action: "list", method: "get"], group: "true") {
   Selector(uid: "downloadType", clocator: [name: "can", id: "can"])
   InputBox(uid: "searchBox", clocator: [name: "q"])
   SubmitButton(uid: "searchButton", clocator: [value: "Search"])
}
```

Tellurium is built on the foundation of the UI module. The UI module makes it possible to build locators for UI elements at runtime.  First, this makes Tellurium robust and responsive to changes from internal UI elements.  Second, the UI module makes Tellurium expressive.  A UI element can be referred to simply by appending the names (uids) along the path to the specific element. This also enables Tellurium's "Group Locating" feature, making composite objects reusable and addressing dynamic web pages.

This frees up the testers to write better tests rather than spend precious testing time on identifying and resolving test failures due to XPath changes.

## Composite Locator ##

Tellurium supports two types of locators: _base locator_ and _composite locator_. The _base locator_ is a relative XPath. The _composite locator_, denoted by "clocator", specifies a set of attributes for the UI object and the actual locator will be derived automatically by Tellurium at runtime.

The Composite Locator is defined as follows:

```
class CompositeLocator {
    String header
    String tag
    String text
    String trailer
    def position
    boolean direct
    Map<String, String> attributes = [:]
}
```

To use the composite locator, you need to use "clocator" with a map as its value. For example:

```
clocator: [key1: value1, key2: value2, ...]
```

The default attributes include "header", "tag", "text", "trailer", "position", and "direct". They are all optional. The "direct" attribute specifies whether this UI object is a direct child of its parent UI, and the default value is "false".

If you have additional attributes, you can define them in the same way as the default attributes, for example:

```
clocator: [tag: "div", value: "Tellurium home"]
```

Most Tellurium objects come with default values for certain attributes, for example, the  tag attribute. If these attributes are not specified, the default attribute values will be used. In other words, if you know the default attribute values of a Tellurium UI object, you can omit them in clocator. Take the RadioButton object as an example, its default tag is "input," and its default type is "radio." You can omit them and write the clocator as follows:

```
clocator: [:]
```

which is equivalent to

```
clocator: [tag: "input", type: "radio"]
```

## Group Locating ##

In the Tellurium UI module, you often see the "group" attribute. For example:

```
ui.Container(uid: "google_start_page", clocator: [tag: "td"], group: "true"){
  InputBox(uid: "searchbox", clocator: [title: "Google Search"])
  SubmitButton(uid: "googlesearch", clocator: [name: "btnG", value: "Google Search"])
  SubmitButton(uid: "Imfeelinglucky", clocator: [value: "I'm Feeling Lucky"])
}
```

What does the attribute "group" mean? The group attribute is a flag for the Group Locating Concept. Usually, the XPath generated by Selenium IDE, XPather, or other tools is a single
path to the target node such as:

```
//div/table[@id='something']/div[2]/div[3]/div[1]/div[6]
```

No sibling node's information is used here. What is wrong with this? The XPath depends too much on information from nodes far away from the target node. In Tellurium, we try to localize the information and reduce this dependency by using sibling information or local information. For example, in the above google UI module, the group locating concept will try to find the "td" tag with its children as "InputBox", "googlesearch" button, and
"Imfeelinglucky" button. In this way, we can reduce the dependencies of the UI elements inside a UI module on external UI elements to make the UI definition more robust.

## UI Templates ##

Tellurium UI templates are used for two purposes:

  1. When there are many identical UI elements, you can use one template to represent them all
  1. When here are a variable/dynamic size of UI elements at runtime, you know the patterns, but not the size.

More specifically, Table and List are two Tellurium objects that can define UI templates. Table defines two dimensional UI templates, and List is for one dimensional. The Template has
special UIDs such as "2", "all", or "row: 1, column: 2".

For use case (2), a common application is the data grid. Look at the "issueResult" data grid on our Tellurium Issues page:

```
ui.Table(uid: "issueResult", clocator: [id: "resultstable", class: "results"], 
         group: "true") 
{
    TextBox(uid: "header: 1",  clocator: [:])
    UrlLink(uid: "header: 2",  clocator: [text: "%%ID"])
    UrlLink(uid: "header: 3",  clocator: [text: "%%Type"])
    UrlLink(uid: "header: 4",  clocator: [text: "%%Status"])
    UrlLink(uid: "header: 5",  clocator: [text: "%%Priority"])
    UrlLink(uid: "header: 6",  clocator: [text: "%%Milestone"])
    UrlLink(uid: "header: 7",  clocator: [text: "%%Owner"])
    UrlLink(uid: "header: 9",  clocator: [text: "%%Summary + Labels"])
    UrlLink(uid: "header: 10", clocator: [text: "%%..."])

    //define table elements
    //for the border column
    TextBox(uid: "row: *, column: 1", clocator: [:])
    //For the rest, just UrlLink
    UrlLink(uid: "all", clocator: [:])
}
```

Aren't the definitions very simple and cool?

You may wonder how to use the templates if you have multiple templates such as the "issueResult" table shown above. The rule to apply the templates is: "specific one first, general one later".

## Javascript Events ##

Most web applications include Javascript, and thus the web testing framework must be able to handle Javascript events. What we really care about is firing the appropriate events to trigger the event handlers.

Selenium has already provided methods to generate events such as:

```
fireEvent(locator, "blur")
fireEvent(locator, "focus")
mouseOut(locator)
mouseOver(locator)
```

Tellurium was born with Javascript events in mind since it was initially designed to test applications written using the DOJO JavaScript framework. For example, we have the following radio button:

```
<input type='radio' name='mas_address_key' value='5779' onClick='SetAddress_5779()'>
```

Although we can define the radio button as follows:

```
RadioButton(uid: "billing", clocator: [name: 'mas_address_key', value: '5779'])
```

The above code will not respond to the click event since the Tellurium RadioButton only supports the "check" and "uncheck" actions, which is enough for the normal case. As a result, no "click" event/action will be generated during the testing. To address this problem, Tellurium added the "respond" attribute to Tellurium UI objects. The "respond" attribute could be used to define whatever events you want the UI object to respond to. The Radio Button can be redefined as:

```
 ui.Container(uid: "form", clocator: [whatever]){
     RadioButton(uid: "billing", clocator: [name: 'mas_address_key', value: '5779'], 
               respond: ["click"])
 }
```

That is to say, you can issue the following command:

```
  click "form.billing"
```

Even if the RadioButton does not have the _click_ method defined by default, it is still able to dynamically add the _click_ method at runtime and call it.

A more general example is as follows:

```
 InputBox(uid: "searchbox", clocator: [title: "Google Search"], 
          respond: ["click", "focus", "mouseOver", "mouseOut", "blur"])
```

Except for the "click" event, all of the "focus", "mouseOver", "mouseOut", and "blur" events will be automatically fired by Tellurium during testing. Do not worry about the event order for the respond attribute, Tellurium will automatically re-order the events and then process them appropriately for you.

## jQuery Selector ##

Starting with version 0.6.0, Tellurium supports a [jQuery selectors](http://code.google.com/p/aost/wiki/TelluriumjQuerySelector) to address the problem of poor performance of XPath in Internet Explorer. Auto-generating jQuery instead of XPath has the following advantages:
  * Faster performance in IE.
  * We are able to use the power of jQuery to call methods on jQuery collections to retrieve bulk data.
  * Tellurium jQuery selector supports the jQuery attribute selectors such as _[attribute!=value]_, _[attribute^=value]_, _[attribute$=value]_, and **[attribute_=value]_.**

To use jQuery selectors, simply call:

```
useJQuerySelector()
```

in you code. To switch back to XPath locators, you should call:

```
disableJQuerySelector()
```

Be aware that the UI module is agnostic to the locate strategy you choose, and thus you do not need to change anything in your test code.

Our Benchmark test results show that:

  1. jQuery selector is as fast as Ajaxslt XPath and Javascript XPath in Firefox 3.
  1. jQuery selector is much faster in IE 7 than the two XPath libraries
  1. jQuery selector uses only one method call to get back data for all table cells, and thus it is the fastest one for bulk data access.

[jQuery cache](http://code.google.com/p/aost/wiki/jQuerySelectorCache) is a mechanism to further improve speed by reusing the previously found DOM reference for a given jQuery selector. Our benchmark results show that the jQuery cache could improve the speed by up to 14% over the regular jQuery selector and over 27% for some extreme cases.

## Domain Specific Language ##

Domain Specific Language (DSL) indicates a programming language or specification language dedicated to a particular problem domain, a particular problem representation technique, or a particular solution technique. Here the domain refers to UI testing. DSL is specified in the class **DslContext**, including UI definitions, actions, and testing.

## Testing Support ##

In the package org.tellurium.test, Tellurium provides three different ways to write Tellurium tests:

  * TelluriumJavaTestCase is for JUnit, and it supports the following JUnit 4 annotations: @BeforeClass, @AfterClass, @Before, @After, @Test, and @Ignore
  * TelluriumTestNGTestCase is for TestNG. Similarly, you can use the following annotations: @BeforeSuite, @AfterSuite, @BeforeTest, @AfterTest, @BeforeGroups, @AfterGroups, @BeforeClass, @AfterClass, @BeforeMethod, @AfterMethod, @DataProvider, @Parameters, and @Test
  * TelluriumGroovyTestCase is for Groovy test cases.
  * Data Driven Testing. Tellurium provides the class _TelluriumDataDrivenModule_ for users to define data driven testing modules and the class _TelluriumDataDrivenTest_ to drive the actual tests.

Apart from the above, Tellurium also provides users the capability to write Tellurium tests and Tellurium data driven tests in pure DSL scripts. The _DslScriptExecutor_ is used to run the .dsl files.

# Tellurium Subprojects #

Tellurium began as a small core project over a year ago and quickly spawned multiple sub-projects including: [Core](http://code.google.com/p/aost/wiki/UserGuide), [Reference projects](http://code.google.com/p/aost/wiki/ReferenceProjectGuide), [Widget extensions](http://code.google.com/p/aost/wiki/TelluriumWidget), [Trump](http://code.google.com/p/aost/wiki/TrUMP), and Engine projects as shown in the following diagram:

http://tellurium-users.googlegroups.com/web/TelluriumSubprojects.png?gda=KOo7BEoAAAD5mhXrH3CK0rVx4StVj0LYlNqOmuvSTE_gHzistV7TYU3GrA4woUylGAUAJF3_rdDbYV_6pXuFBwitfyYTkV3q_e3Wg0GnqfdKOwDqUih1tA&gsc=JDDH5gsAAACcJEKey8RXDXypkrHyWRpX

  * Tellurium Engine: Based on Selenium Core with UI module, jQuery selectors, command bundle, and exception hierarchy support.
  * Tellurium Core: UI module, APIs, DSL, Object to Runtime Locator mapping, and test support.
  * Tellurium Extensions: Dojo Javascript widgets and ExtJS Javascript widgets.
  * Tellurium UI Module Plugin (Trump): A Firefox plugin to automatically generate the UI module after users select the UI elements from the web under testing.
  * Tellurium Maven Archetypes: Maven archetypes to generate skeleton Tellurium JUnit and Tellurium TestNG projects using one Maven command.
  * Tellurium Reference Projects: Use Tellurium project site as examples to illustrate how to use different features in Tellurium and how to create Tellurium test cases.

Tellurium Core has been described in the above section, and here we would like to elaborate more on Tellurium Extensions, the Tellurium UI Module Plugin, and Tellurium Engine.

## Tellurium Extensions ##

[Tellurium Widget](http://code.google.com/p/aost/wiki/TelluriumWidget) is a good way to re-use UI components in testing. Usually, Javascript frameworks provide a lot of widgets. Take the Dojo framework as an example; you can define the DatePicker Dojo widget just like a regular Tellurium UI module. For widgets, it is important to include name space to avoid name collision between different widget modules. If we use "DOJO" as the name space, the widget could be written like "DOJO\_DatePicker". We can define the following methods for the DOJO Date Picker widget:

```
    public String getCurrentYear(){
        return getText("DatePicker.year.currentYear")
    }

    public void selectPrevYear(){
        click "DatePicker.year.prevYear"
    }
```

To use the Date Picker widget, we can include the compiled jar file and include it like a regular tellurium UI object. For example:

```
class DatePickerDemo extends DslContext{
    
    public void defineUi() {
        ui.Form(uid: "dropdown", clocator: [:], group: "true"){
            TextBox(uid: "label", clocator: [tag: "h4", text: "Dropdown:"])
            InputBox(uid: "input", clocator: [dojoattachpoint: "valueInputNode"])
            Image(uid: "selectDate", clocator: [title: "select a date", 
                            dojoattachpoint: "containerDropdownNode", alt: "date"])
            DOJO_DatePicker(uid: "datePicker", clocator: [tag: "div", 
                            dojoattachpoint: "subWidgetContainerNode"])
        }
    }
}
```

To make the framework scalable, Tellurium provides an _onWidget_ method for Tellurium widgets. In this way we can call the widget methods as follows:

```
onWidget "dropdown.datePicker", selectPrevYear
```

## Tellurium UI Module Plugin (Trump) ##

The Tellurium UI Module Plugin (Trump) is used to automatically generate UI modules for users. The Trump IDE looks as follows:

http://tellurium-users.googlegroups.com/web/TrUMPIDE0.1.0.png?gda=EVgeNEMAAAD5mhXrH3CK0rVx4StVj0LYBWucT0-vkPMbIT-o-BeAq7267uE5ld_gRd1XTkHcEWoytiJ-HdGYYcPi_09pl8N7FWLveOaWjzbYnpnkpmxcWg

The workflow of Trump is shown here:


http://tellurium-users.googlegroups.com/web/TrUMPDiagramSmall.png?gda=9CFDmEcAAAD5mhXrH3CK0rVx4StVj0LYmqln6HzIDYRu0sy-jUnaq8nAtNENvOA6NOHkUCAqlOUVeY4b49xGcMK802iZZ8SFeV4duv6pDMGhhhZdjQlNAw&gsc=5FPzPwsAAAA9y3PlQReYHIKRUJU7LIYD

  1. First, the user clicks on a web page, and the corresponding UI element is pushed into an array. If the user clicks the element again, the UI element is removed from the array.
  1. When the user clicks on the "Generate" button, Trump does the following two steps. First, Trump generates an internal tree to represent the UI elements using a grouping algorithm. During the tree generating procedure, extra nodes are generated to group UI elements together based on their corresponding location on the DOM tree. The internal tree is very useful and holds all original data that can be used for customization. Once the internal tree is built, Trump starts the second step, which is to build the default UI module. For each node in the internal tree, Trump generates a UI object based on its tag and whether or not it is a parent node.
  1. When the user clicks on the "Customize" button, Trump will pull out the original data held in the internal tree and the current attributes utilized by the UI module to create the "Customize" view. When the user clicks on an element, Trump lists all available optional attributes in the view for users to customize.
  1. Trump will try to validate the UI module automatically whenever you generate a new UI module or update it. Trump will evaluate each UI element's XPath the same way that Tellurium generates the runtime XPath from the UI module and check if the generated runtime XPath is unique in the current web page. If it is not unique, you will see a red "X" mark, and you need to modify the element's attribute to make it disappear. If you do not see a red "X", you are finished. You can export the generated UI module to a groovy file and start to write Tellurium tests based on the generated UI module.

## Tellurium Engine ##

Up to Tellurium 0.6.0, Tellurium still leveraged Selenium core as the test driving engine. Selenium has a rich set of APIs to act on individual UI elements, and Tellurium can use them directly. Tellurium embedded its own small engine in Selenium core to support the following enhancements:

  * jQuery selector support by adding a new jQuery selector locate strategy so that the Selenium core can handle the jQuery selectors passed in from Tellurium core.
  * jQuery selector caching to increase the reuse of previously located DOM references and reduce the UI element locating time.
  * Bulk data retrieval. For example, the following method will get back all data in a table with just one method call, which improves the speed dramatically:

```
String getAllTableCellText(String uid) 
```

  * New APIs for partial matching of attributes, CSS query, and more.

Using Selenium core as the test driving engine is a quick solution for Tellurium, but it suffers some drawbacks such as low efficiency because Selenium core does not have built-in support for UI modules. We are working on our new engine to gradually replace Selenium core in order to achieve the following goals:

  * Command bundle to reduce round-trip time between Tellurium core and the Tellurium Engine.
  * UI module caching at the Engine side to reuse the discovered locator in the UI module no matter if the locator is XPath or a jQuery selector.
  * Group locating at the Engine side to exploit more information about the UI elements in the UI module to help locate UI elements
  * Exception hierarchy so that the Engine returns meaningful error codes back to the Tellurium core.
  * Ajax response notification.


# How to Obtain and Use Tellurium #

## Create a Tellurium Project ##

There are three ways: use [the reference project](http://code.google.com/p/aost/wiki/ReferenceProjectGuide) as a base, use the [Tellurium Maven archetype](http://code.google.com/p/aost/wiki/TelluriumMavenArchetypes), or manually create a Tellurium project using the [tellurium jar](http://code.google.com/p/aost/downloads/list) and a [Tellurium configuration file](http://code.google.com/p/aost/wiki/TelluriumSampleConfigurationFile). Alternatively, you could create your own Tellurium Maven project manually using [the sample POM file](http://code.google.com/p/aost/wiki/TelluriumTestProjectMavenSamplePom).

The easiest way to create a Tellurium project is to use Tellurium Maven archetypes. Tellurium provides two Maven archetypes: tellurium-junit-archetype and tellurium-testng-archetype (for Tellurium JUnit test projects and Tellurium TestNG test projects respectively.) As a result, you can create a Tellurium project using one Maven command. For a Tellurium JUnit project, use:

mvn archetype:create -DgroupId=your\_group\_id -DartifactId=your\_artifact\_id -DarchetypeArtifactId=tellurium-junit-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.7.0-SNAPSHOT -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/snapshots

and for a Tellurium TestNG project, use:

mvn archetype:create -DgroupId=your\_group\_id -DartifactId=your\_artifact\_id -DarchetypeArtifactId=tellurium-testng-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.7.0-SNAPSHOT -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/snapshots

## Create a UI Module ##

Tellurium provides Trump for you to automatically create UI modules. Trump can be downloaded from the Tellurium project site:

http://code.google.com/p/aost/downloads/list

Choose the Firefox 2 or Firefox 3 version depending on your Firefox version, or you can download the Firefox 3 version directly from the Firefox addons site at:

https://addons.mozilla.org/en-US/firefox/addon/11035

Once you install it and restart Firefox, you are ready to record your UI modules by simply clicking on the UI element on the web and then clicking the "generate" button. You may like to customize your UI a bit by clicking the "Customize" button. More detail:

In our example, we open up Tellurium download page

http://code.google.com/p/aost/downloads/list

and record the download search module as follows:

http://tellurium-users.googlegroups.com/web/TrUMPRecordDownloadPageSmall.png?gda=aeAak1IAAAD5mhXrH3CK0rVx4StVj0LYmqln6HzIDYRu0sy-jUnaq73MhpfIdLRuVAwbLjuTZtQ7YQyGo5rEgT7iH53cuUInVeLt2muIgCMmECKmxvZ2j4IeqPHHCwbz-gobneSjMyE&gsc=5FPzPwsAAAA9y3PlQReYHIKRUJU7LIYD

After we customize the UI module, we export it as the module file NewUiModule.groovy to the demo project and add a couple of methods to the class:

```
class NewUiModule extends DslContext {

  public void defineUi() {
    ui.Form(uid: "TelluriumDownload", clocator: [tag: "form", method: "get", action: "list"], 
           group: "true") 
{
      Selector(uid: "DownloadType", clocator: [tag: "select", name: "can", id: "can"])
      InputBox(uid: "Input", clocator: [tag: "input", type: "text", name: "q", id: "q"])
      SubmitButton(uid: "Search", clocator: [tag: "input", type: "submit", value: "Search"])
    }
  }

  //Add your methods here
  public void searchDownload(String keyword) {
    keyType "TelluriumDownload.Input", keyword
    click "TelluriumDownload.Search"
    waitForPageToLoad 30000
  }

  public String[] getAllDownloadTypes() {
    return getSelectOptions("TelluriumDownload.DownloadType")
  }

  public void selectDownloadType(String type) {
    selectByLabel "TelluriumDownload.DownloadType", type
  }
}
```

## Create Tellurium Test Cases ##

Once you create the UI module, you can create a new Tellurium test case NewTestCase by extending TelluriumJavaTestCase class.

```
public class NewTestCase extends TelluriumJavaTestCase {
    private static NewUiModule app;

    @BeforeClass
    public static void initUi() {
        app = new NewUiModule();
        app.defineUi();
    }

    @Before
    public void setUpForTest() {
        connectUrl("http://code.google.com/p/aost/downloads/list");
    }

    @Test
    public void testTelluriumProjectPage() {
        String[] allTypes = app.getAllDownloadTypes();
        assertNotNull(allTypes);
        assertTrue(allTypes[1].contains("All Downloads"));
        app.selectDownloadType(allTypes[1]);
        app.searchDownload("TrUMP");
    }
}
```

Compile the project and run the new test case.

# Summary and Future Plans #

Tellurium is robust, expressive, flexible, and reusable with many unique features. Tellurium introduces a new approach to test web applications based on UI modules.
We have released Tellurium 0.6.0 and are working on 0.7.0.

Tellurium will continue evolving, and we will focus on the following tasks:

  * Develop the Engine project in 0.7.0 to provide better support for UI modules and achieve better re-usability, speed performance, and robust responsiveness to change.
  * Tellurium widgets will be re-architected to encapsulate dynamic factors and to make them easier to define and use.
  * The widget extension projects will be implemented to include many widgets from Javascript frameworks such as Dojo and ExtJS so that users can include these widgets in their UI modules simply by their names without the need to know the details of the internal UI elements.
  * Trump will be improved to support frames, pop-up widows, and UI templates. Another big move will be to automatically create UI modules as well as DSL test scripts for non-developers.
  * To better support functional tests, we will introduce a "stage and test binding" concept to make functional tests easier to write and configure.
  * Headless web testing using HtmlUnit could be another interesting topic.
  * Other initiatives include behavior driven test support, IDE plugins, XHTML support, integration with other testing framework like Fitnesse, and porting to other languages.

# Acknowledgments #

# Resources #

  * [Tellurium Project website](http://code.google.com/p/aost/)
  * [Tellurium User Group](http://groups.google.com/group/tellurium-users)
  * [Tellurium User Guide](http://code.google.com/p/aost/wiki/UserGuide)
  * [Tellurium UI Model Firefox Plugin (TrUMP) 0.1.0](http://code.google.com/p/aost/wiki/TrUMP)
  * [Selenium](http://seleniumhq.org/)
  * [Canoo WebTest](http://webtest.canoo.com)
  * [Twill](http://twill.idyll.org/)
  * [Groovy](http://groovy.codehaus.org/)
  * [JQuery](http://jquery.com/)
  * [JUnit](http://www.junit.org/)
  * [TestNG](http://testng.org/doc/documentation-main.html)
  * [HtmlUnit](http://htmlunit.sourceforge.net)