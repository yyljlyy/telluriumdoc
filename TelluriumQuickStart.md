

# 简介 #

Tellurium是一个开源的网页测试框架，现阶段还是建立在Selenium之上。 但有很多独特的测试理念。 比之Selenium, 维护性，鲁棒性， 和可复用性都要好。 它支持JUnit和TestNG。Tellurium的测试代码可以用Java, Groovy和纯DSL脚本来写.

## Tellurium子项目 ##

Tellurium由以下子项目构成：
  * Tellurium Core: 处理DSL, 自动生成Runtime Locator，和测试案例的支持。
  * Tellurium Engine: 是Tellurium的底层测试驱动模块
  * Tellurium Widget: 是Tellurium的扩展，可以定义Javascript库, 如Dojo和ExtJS,的Widget，以便重用。
  * Tellurium UI Module Plugin (TrUMP): 是个Firefox插件，能够让用户在待测网页上选择所需的UI元素，然后自动生成Tellurium UI模块。
  * Tellurium Reference Project: 是Tellurium的参考项目，测试代码是为Tellurium项目的网页而写的。主要向用户展示如何定义自己的Tellurium物件(Object), 如何写Tellurium测试代码，数据驱动代码，和纯DSL脚本。参考项目有两个，分别为JUnit项目和TestNG项目。
  * Tellurium Maven Archetypes: 方便用户创建自己的Tellurium测试项目。有两个Archetype,为JUnit项目和TestNG项目而设。

http://tellurium-users.googlegroups.com/web/TelluriumProjects.png?gsc=8WCcfRYAAABueqEHekK8LxR3X1leEByKg-kXU5InE09W2o0GCSVgCQ

## 一点Tellurium概念 ##

虽然Tellurium脱胎于Selenium,但两者在概念上有很大的不同。Selenium主要是用记录和重播(Record and Replay)模式，而Tellurium要求你首先定义待测的UI模块。下面列出Tellurium的一些主要概念：

### Tellurium物件 ###

Tellurium定义了一些常用的物件，如Button, Table, Form等。

Tellurium物件至少包含以下属性：

  * uid: 物件的引用名
  * namespace： 用于XHTML，对HTML它总是null
  * locator: 用于定位物件相对应的UI元素在网页DOM中的位置。它会是XPath或由一系列的属性构成，如HTML tag, type, value等等。 下节会更详细介绍。

| **Tellurium物件** | **Locator缺省属性** | **额外属性** | **UI模板** |
|:--------------------|:------------------------|:-----------------|:-------------|
| Button              | tag: "input"            |                  |              |
| Container           |                         | group            |              |
| CheckBox            | tag: "input", type: "checkbox" |                  |              |
| Div                 | tag: "div"              |                  |              |
| Form                | tag: "form"             | group            |              |
| Image               | tag: "img"              |                  |              |
| InputBox            | tag: "input"            |                  |              |
| RadioButton         | tag: "input", type: "radio" |                  |              |
| Selector            | tag: "select"           |                  |              |
| Span                | tag: "span"             |                  |              |
| SubmitButton        | tag: "input", type: "submit" |                  |              |
| UrlLink             | tag: "a"                |                  |              |
| List                |                         | separator        | 可用       |
| Table               | tag: "table"            | group, header    | 可用       |
| StandardTable       | tag: "table"            | group, header, footer | 可用       |
| Frame               |                         | group, id, name, title |              |
| Window              |                         | group, id, name, title |              |

值得注意的是Tellurium物件大多为抽象物件，比如Container就可以实用于不同的HTML标签。每个Tellurium物件都有一个uid，即它的别名。

### Locator ###

Locator是用来定位一个元素在网页DOM中的位置，Tellurium支持两种Locator, 一种叫 _Base Locator_ , 用“locator”标识，它是一个相对的XPath. 如：
```
InputBox(uid: "SearchBox", locator: "//input[@title='Google Search']")
```

另一个是 _Composite Locator_ , 用“clocator”来标识，它是由一组属性构成，如
```
InputBox(uid: "SearchBox", clocator: [title: "Google Search"])
```

_Composite Locator_ 是Tellurium的缺省Locator, 它容易写，表叙性好，方便动态生成Runtime Locator.

### UI模块 ###

UI模块是一个复合的Tellurium物件，它是由一些单个Tellurium物件嵌套构成。一个UI模块往往代表DOM的一个子树。比如Google首页搜索模块就可以表叙为

```
ui.Container(uid: "GoogleSearchModule", clocator: [tag: "td"], group: "true"){
   InputBox(uid: "Input", clocator: [title: "Google Search"]，respond: ["focus", "mouseOver"])
   SubmitButton(uid: "Search", clocator: [name: "btnG", value: "Google Search"])
   SubmitButton(uid: "ImFeelingLucky", clocator: [value: "I'm Feeling Lucky"])
}
```

UI模块的最外层元素以定要以"ui."开始。这里group为“true”表示Tellurium用利用UI元素之间的关系来协助它们在网页DOM中的定位。respond属性定义了InputBox需要触发“focus”和“mouseOver”事件，Tellurium会自动触发这些事件的。

### Tellurium测试案例 ###

Tellurium测试代码可以用Java, Groovy, 或纯DSL脚本来写。无论哪种方法，Tellurium要求你定义独立的UI模块，使之和测试代码分离，以便于维护。UI模块必须继承Tellurium的DslContext class, 一般你还需定义对UI模块的操作方法。例如：

```
class GoogleSearchModule extends DslContext{

   public void defineUi() {
         ui.Container(uid: "google_start_page", clocator: [tag: "td"], group: "true"){
            InputBox(uid: "searchbox", clocator: [title: "Google Search"])
            SubmitButton(uid: "googlesearch", clocator: [name: "btnG", value: "Google Search"])
            SubmitButton(uid: "Imfeelinglucky", clocator: [value: "I'm Feeling Lucky"])
        }
   }

   def doGoogleSearch(String input){
        keyType "searchbox", input
        pause 500
        click "googlesearch"
        waitForPageToLoad 30000
    }

    def doImFeelingLucky(String input){
        type "searchbox", input
        pause 500
        click "Imfeelinglucky"
        waitForPageToLoad 30000
    }
}
```

如果你的测试案例用JUnit来写，你需要继承TelluriumJavaTestCase.

```
public class GoogleSearchTestCase extends TelluriumJavaTestCase {
    private static GoogleSearchModule gsm;
    
    @BeforeClass
    public static void initUi() {
        gsm = new GoogleSearchModule();
        gsm.defineUi();
    }

    @Before
    public void connectToGoogle() {

        connectUrl("http://www.google.com");
    }

    @Test
    public void testGoogleSearch() {
        gsm.doGoogleSearch("tellurium . ( Groovy ) Test");
    }

    @Test
    public void testGoogleSearchFeelingLucky() {
        gsm.doImFeelingLucky("tellurium automated Testing");
    }
}
```

TestNG的案例类似，除了你要继承TelluriumTestNGTestCase。如果用Groovy, 你需要继承TelluriumGroovyTestCase.

### Tellurium配置 ###

Tellurium用一个配置文件TelluriumConfig.groovy来配置系统。它包括对Selenium服务器和Tellurium框架本身的配置，如：

```
tellurium{
    //embedded selenium server configuration
    embeddedserver {
        //port number
        port = "4445"
        //whether to use multiple windows
        useMultiWindows = false
        runInternally = true
        profile = ""
    }
    //event handler
    eventhandler{
        //whether we should check if the UI element is presented
        checkElement = true
        //wether we add additional events like "mouse over"
        extraEvent = true
    }
    //data accessor
    accessor{
        //whether we should check if the UI element is presented
        checkElement = false
    }
    connector{
        //selenium server host
        //please change the host if you run the Selenium server remotely
        serverHost = "localhost"
        //server port number the client needs to connect
        port = "4445"
        //base URL
        baseUrl = "http://localhost:8080"
        //Browser setting, valid options are
        //  *firefox [absolute path]
        //  *iexplore [absolute path]
        //  *chrome
        //   *iehta

        browser = "*chrome"
    }
    datadriven{
        dataprovider{
            reader = "PipeFileReader"
        }
    }
    test{
        result{
            reporter = "XMLResultReporter"
            output = "Console"
            filename = "TestResult.output"
        }
        exception{
            captureScreenshot = true
            filenamePattern = "Screenshot?.png"
        }
    }
    uiobject{
        builder{
            //example:
           SelectMenu="org.tellurium.builder.SelectMenuBuilder"
        }
    }
    widget{
        module{
            //define your widget modules here, for example Dojo or ExtJs
            included=""
        }
    }
}
```

此外，Tellurium还提供了方法让用户来覆盖Tellurium中的配置，

```
public class GoogleSearchModuleTestCase extends TelluriumJavaTestCase
{
   static{
       setCustomConfig(true, 5555, "*chrome", true, null);
   }

...

}
```

## 运行Tellurium的条件 ##

由于Tellurium的UI模块必须是一个Groovy class, 你需要Groovy才能运行Tellurium. 请从[Groovy官方网页](http://groovy.codehaus.org/Download)下载Groovy，当然，你也可以用Maven下载。

在IDE中运行Tellurium, 你需要安装Groovy插件。Eclipse的Groovy插件在: http://dist.codehaus.org/groovy/distributions/update/. 新版的Netbeans和IntelliJ本身就带有Groovy插件。

# 创建自己的Tellurium测试项目 #

http://tellurium-users.googlegroups.com/web/HowToUseTellurium.png?gda=E2fneEcAAACXZPxEX7Ki-M5C2JpeBoXXwOvr7XA0t7SnOHKVzf4DhFd6vDxrTQI8X2xdNkWs9mIVeY4b49xGcMK802iZZ8SFeV4duv6pDMGhhhZdjQlNAw&gsc=YyVqmwsAAABmHtz3tZj6NRBcOVGYgXTk

有三种方法创建自己的Tellurium测试项目。一是从[Tellurium下载网页](http://code.google.com/p/aost/downloads/list)下载文件手工创建，这样比较费事。或者下载Tellurium的参考项目，用它作为你的模板项目。更方便的方法是用Tellurium的Maven archetype来创建，非常简单，一个Maven命令就完事, 比如：

```
mvn archetype:create -DgroupId=example -DartifactId=demo -DarchetypeArtifactId=tellurium-junit-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.6.0  -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/releases
```

这个命令创建了Tellurium JUnit 测试项目。然后用编辑器如IntelliJ IDEA打开项目用如下部步骤，

```
New Project > Import project from external model > Maven > Project directory > Finish
```

你会发现项目中有以下文件
```
pom.xml
src
src/main
src/main/groovy
src/main/resources
src/test
src/test/groovy
src/test/groovy/module
src/test/groovy/module/GoogleSearchModule.groovy
src/test/groovy/test
src/test/groovy/test/GoogleSearchTestCase.java
src/test/resources
TelluriumConfig.groovy
```

其中TelluriumConfig.groovy是Tellurium配置文件， 而GoogleSearchModule是示范网页模块文件， 而GoogleSearchTestCase是示范测试文件。检查项目的Groovy配置，确保是1.6.0版本。 然后就可以用以下Maven命令来运行示范测试文件了，

```
mvn test
```

如果你想创建TestNG项目，可以用以下Maven命令：

```
mvn archetype:create -DgroupId=your_group_id -DartifactId=your_artifact_id -DarchetypeArtifactId=tellurium-testng-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.6.0 -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/releases
```

# 创建自己的UI模块 #

Tellurium的Firefox Plugin(TrUMP)可以用来自动创建网页模块文件， 然后你加入测试逻辑就行了。 TrUMP可以从Tellurium网站下载

http://code.google.com/p/aost/downloads/list

或者从Firefox addons直接下载

https://addons.mozilla.org/en-US/firefox/addon/11035

TrUMP简单易用，具体的操作这里就不细说了。

http://tellurium-users.googlegroups.com/web/TrUMPRecordDownloadPage.png?gda=OlvnAU0AAACXZPxEX7Ki-M5C2JpeBoXX_AfRGjKOg8bfAG7WWa1zzr3MhpfIdLRuVAwbLjuTZtSqonpmmrv0I-p7nCVZQmP65Tb_vjspK02CR95VRrtmeQ&gsc=r5ESUAsAAAB_6XgBAY-OTO2KhvY2ZJUe

# 相关资源 #

  * Tellurium项目网址: http://code.google.com/p/aost/
  * Tellurium用户组: http://groups.google.com/group/tellurium-users
  * 在线幻灯片："[Tellurium - A New Approach For Web Testing](http://www.slideshare.net/John.Jian.Fang/telluriumanewapproachforwebtesting)"
  * Screencast视频: "[Ten Minutes to Tellurium](http://www.youtube.com/watch?v=DyUPeg-Y-Yg)"