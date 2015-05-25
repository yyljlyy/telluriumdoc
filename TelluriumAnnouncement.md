

# Tellurium公告 #

## Tellurium 0.6.0 发布了 ##

Tellurium 0.6.0 包括以下模块:

  * Tellurium Core 0.6.0
  * Tellurium JUnit 参考项目 0.6.0
  * Tellurium TestNG 参考项目 0.6.0
  * Tellurium JUnit Maven Archetype 0.6.0
  * Tellurium TestNG Maven Archetype 0.6.0
  * Customized Selenium Server 1.0.1-te

### 新特性 ###

Tellurium 0.6.0 是一个具有很多新特性的一个版本。其中最大的更新包括增加对jQuery selector的支持，jQuery Selector缓存, Selenium Grid支持, 用于批量数据处理的新的API和无数其他的更新. 具体内容青参阅["Whats New in Tellurium 0.6.0"](http://code.google.com/p/aost/wiki/WhatsNewInTellurium_0_6_0).

同时，我们改变了selenium-core 1.0.1，在其中加入了我们自己的Engine代码来支持jQuery selector和新的API.

### 如何获取Tellurium 0.6.0 ###

如果你对Maven熟悉， 最简便的方法是用Tellurium Maven archetypes, 它包括tellurium-junit-archetype和tellurium-testng-archetype，分别用于创建Tellurium JUnit项目和Tellurium TestNG项目.

例如，创建Tellurium JUnit项目，可用以下Maven命令,

```
mvn archetype:create -DgroupId=your_group_id -DartifactId=your_artifact_id -DarchetypeArtifactId=tellurium-junit-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.6.0 -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/releases
```

而Tellurium TestNG项目，则应该用，

```
mvn archetype:create -DgroupId=your_group_id -DartifactId=your_artifact_id -DarchetypeArtifactId=tellurium-testng-archetype -DarchetypeGroupId=tellurium -DarchetypeVersion=0.6.0 -DarchetypeRepository=http://kungfuters.org/nexus/content/repositories/releases
```

被创建的项目带了一个测试例子, GoogleSearchTestCase. 你可以进入项目目录，然后运行下面命令去运行它,

```
mvn test
```

如果你想把Tellurium 0.6.0加到一个已有的项目中, 需要把以下内容加到你的POM文件中,

```
   <repositories>
        <repository>
            <id>caja</id>
            <url>http://google-caja.googlecode.com/svn/maven</url>
        </repository>
        <repository>
            <id>kungfuters-public-releases-repo</id>
            <name>Kungfuters.org Public Releases Repository</name>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://kungfuters.org/nexus/content/repositories/releases</url>
        </repository>
        <repository>
            <id>kungfuters-thirdparty-releases-repo</id>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://kungfuters.org/nexus/content/repositories/thirdparty</url>
        </repository>
        <repository>
            <id>openqa-release-repo</id>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <url>http://archiva.openqa.org/repository/releases</url>
        </repository>
    </repositories>

    <dependencies>
        <dependency>
          <groupId>org.openqa.selenium.grid</groupId>
          <artifactId>selenium-grid-tools</artifactId>
          <version>1.0.2</version>
        </dependency> 

        <dependency>
            <groupId>tellurium</groupId>
            <artifactId>tellurium-core</artifactId>
            <version>0.6.0</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.seleniumhq.selenium.server</groupId>
            <artifactId>selenium-server</artifactId>
            <version>1.0.1-te</version>
        </dependency>
        <dependency>
            <groupId>org.seleniumhq.selenium.client-drivers</groupId>
            <artifactId>selenium-java-client-driver</artifactId>
            <version>1.0.1</version>
            <exclusions>
                <exclusion>
                    <groupId>org.codehaus.groovy.maven.runtime</groupId>
                    <artifactId>gmaven-runtime-default</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.seleniumhq.selenium.core</groupId>
                    <artifactId>selenium-core</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.seleniumhq.selenium.server</groupId>
                    <artifactId>selenium-server</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-all</artifactId>
            <version>1.6.0</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>caja</groupId>
            <artifactId>json_simple</artifactId>
            <version>r1</version>
        </dependency>
        <dependency>
            <groupId>org.stringtree</groupId>
            <artifactId>stringtree-json</artifactId>
            <version>2.0.10</version>
        </dependency>
    </dependencies>
```

这里是一个［http://code.google.com/p/aost/wiki/TelluriumTestProjectMavenSamplePom 示范的POM文件] 以供你参考.

对Ant用户，你需要从[Tellurium项目下载页](http://code.google.com/p/aost/downloads/list)来下载[Tellurium core 0.6.0 jar](http://aost.googlecode.com/files/tellurium-core-0.6.0.jar), [Tellurium相关文件](http://aost.googlecode.com/files/tellurium-0.6.0-dependencies.zip), 和 [Tellurium配置文件](http://code.google.com/p/aost/wiki/TelluriumSampleConfigurationFile). 你要把Tellurium相关文件解压到项目的/lib目录下，并把Tellurium core 0.6.0 jar放到同一路径下. 并将Tellurium配置文件命名为TelluriumConfig.groovy，然后放到项目根目录下.

这里是[示范的Ant脚本](http://code.google.com/p/aost/wiki/TelluriumSampleAntBuildScript)以供你参考。

### Tellurium参考项目 ###

如果你刚接触Tellurium, [Tellurium参考项目](http://code.google.com/p/aost/wiki/ReferenceProjectGuide)是你的好的学习起点。Tellurium参考项目包括[tellurium-junit-java](http://aost.googlecode.com/files/reference-junit.0.6.0.tar.gz)和[tellurium-testng-java](http://aost.googlecode.com/files/reference-testng.0.6.0.tar.gz), 你需要从[Tellurium项目下载页](http://code.google.com/p/aost/downloads/list)来下载它们。

这两个项目基本相同，唯一的不同是前者用JUnit 4而后者用TestNG.

Tellurium参考项目相你展示了Tellurium的以下用途：

  * 如何创建一个Tellurium项目
  * 如何定义自己的Tellurium物件
  * 如何定义UI module
  * 如何用JUnit或TestNG来写Tellurium测试代码
  * 如何创建DSL脚本
  * 如何创建Tellurium数据驱动测试
  * 如何配置Tellurium项目
  * Ant脚本
  * Maven POM文件

### 反馈 ###

如果你有任何问题或建议，请发到[Tellurium用户组](http://groups.google.com/group/tellurium-users)。