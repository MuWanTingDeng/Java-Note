# [#](#Maven简介) Maven简介

Maven的本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型（POM)）

这玩意儿是使用Java开发的，所以采用的就是Java的思想：面向对象

POM (Project Object Model)：项目对象模型
<img src="https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621205944792-1316215648.png" alt="截屏2021-07-17 下午6.51.07"  />



Maven的作用：

1. 项目构建：提供标准的、跨平台的自动化项目构建方式
2. 依赖管理：方便快捷的管理项目依赖的资源（jar包），避免资源间的版本冲突问题
3. 统一开发结构：提供标准的、统一的项目结构



下载与安装：

- 官网：http://maven.apache.org/
- 下载：http://maven.apache.org/download.cgi



# [#](#Maven基础概念) Maven基础概念

![image-20240621211654330](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621211656904-1492644179.png)



仓库：用于存储资源，包含各种jar包

仓库分类：本地仓库和远程仓库（私服和中央仓库）

坐标：Maven中的坐标用于描述仓库中资源的位置

坐标的主要组成：

- groupId：定义当前Maven项目隶属组织名称（通常是域名反写）
- artifactId：定义当前Maven项目名称（通常是模块名称）
- version：定义当前版本号
- packaging：定义该项目的打包方式

坐标的作用：使用唯一的标识，唯一性定位资源位置，通过该标识可以将资源的识别与下载交由机器完成。

仓库配置：

- 本地仓库配置：默认位置与自定义位置
- 远程仓库配置：

```xml
<repositories> 
    <repository>
    <id>central</id>
    <name>Central Repository</name>
    <url>https://repo.maven.apache.org/maven2</url>
    <layout>default</layout>
    <snapshots>
        <enabled>false</enabled>
    </snapshots>
    </repository>
</repositories>
```

镜像仓库配置：[阿里云Maven镜像仓库发布与配置地址](https://developer.aliyun.com/mvn/guide)

```xml
<mirrors>
    <mirror>
    <!-- 此镜像的唯一标识符，用来区分不同的mirror元素 -->
	<id>nexus-aliyun</id>
    <!-- 对那种仓库进行镜像（就是替代哪种仓库）-->
	<mirrorOf>central</mirrorOf>
    <!-- 镜像名称 -->
	<name>Nexus aliyun</name>
    <!-- 镜像URL -->
	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror> 
</mirrors>
```



全局setting和用户setting

- 定义当前计算机中Maven的公共配置，即 mavne安装目录/conf/setting.xml
- 定义当前用户配置，即 配置的本地仓库地址处有个平级的setting.xml

注：用户setting和全局setting不一致时，会优先采用用户setting.xml的配置，因此最好是这二者内容保持一致



# [#](#Maven项目) Maven项目

## [#](#手动生成Maven项目) 手动生成Maven项目

Maven工程目录结构

<img src="https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621205946120-1581359391.png" alt="截屏2021-07-17 下午6.50.36"  />



Maven项目构建命令：Maven构建命令使用mvn开头，后面加功能参数，可以一次执行多个命令，使用空格分隔

```bash
mvn compile 	# 编译
mvn clean	 	# 清理
mvn test 		# 测试
mvn package		# 打包
mvn install 	# 安装到本地仓库
```

## [#](#IDEA生成Maven项目) IDEA生成Maven项目

使用原型创建Maven项目与不使用原型创建Maven项目

例：使用原型创建web项目，选择archetype-webapp进行项目创建，添加Tomcat插件

```xml
<build> 
    <plugins>
        <plugin> 
            <groupId>org.apache.tomcat.maven</groupId> 
            <artifactId>tomcat7-maven-plugin</artifactId> 				
            <version>2.1</version>
            <configuration>
                <port>80</port>
                <path>/</path> 
            </configuration>
        </plugin> 
    </plugins>
</build>
```



# [#](#依赖管理) 依赖管理

1. 依赖配置：依赖指的是当前项目运行所需要的jar，一个项目可以设置多个依赖。格式：

   ```xml
   <!--设置当前项目所依赖的所有jar-->
   <dependencies>
     <!--设置具体的依赖-->
     <dependency>
       <!--依赖所属群组id-->
       <groupId></groupId>
       <!--依赖所属项目id-->
       <artifactId></artifactId>
       <!--依赖版本号-->
       <version></version>
       <!--
   		<type>pom</type>
   		<scope>import</scope>
   		只能用在 <dependencyManagement></dependencyManagement> 中
   	-->
       <!--类型：jar 则导入jar包	pom 导入的是一个父模块-->
       <type></type>
       <!--
   		作用域：import 代表把父模块中的jar包导入进来
   		为import时，dependency不参与依赖传递
   				   只是把dependency需要的依赖都取过来，像个占位符一样替换了就行
   	-->
       <scope>import</scope>  
     </dependency>
   </dependencies>
   ```

   

2. 依赖传递

依赖具有传递性，包括直接传递和间接传递

直接传递：在当前项目中通过依赖配置建立的依赖关系（A使用B，A和B就是直接传递）

间接传递：被依赖的资源如果依赖其他资源，当前项目间接依赖其他资源（比较拗口，意思是如果A依赖B，而B依赖C，那么

A和C之间就是间接传递）

![img](https://img2023.cnblogs.com/blog/2421736/202407/2421736-20240707152516668-514480604.png)



依赖传递的冲突问题：

- 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
- 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
- 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的



1. 可选依赖


可选依赖指的是对外隐藏当前所依赖的资源

```xml
<dependency>
  <groupId></groupId>
  <artifactId></artifactId>
  <version></version>
  <!--添加下面这一行-->
  <optional>true</optional>
</dependency>
```

1. 排除依赖


排除依赖指主动断开依赖的资源，被排除的资源无需指定版本

```xml
<dependency>
  <groupId></groupId>
  <artifactId></artifactId>
  <version></version>
  <exclusions>
    <exclusion>
      <groupId></groupId>
      <artifactId></artifactId>
    </exclusion>
  </exclusions>
</dependency>
```



1. 依赖范围


依赖的jar包默认情况可以在任何地方使用，可以通过scope标签设定其作用范围

作用范围：

- 主程序范围有效（main文件夹范围内）
- 测试程序范围有效（test文件夹范围内）
- 是否参与打包（package文件夹范围内）

<img src="https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621205948809-1738593283.png" alt="截屏2021-07-17 下午7.10.15"  />

还有两个：system、import

- import：依赖项替换为指定 POM 的 `<dependencyManagement>`中的该依赖项。仅 `<dependencyManagement>` 部分中 pom 类型的依赖项支持此范围



6. 依赖范围传递性：带有依赖范围的资源在传递时，作用范围会受到影响



![image-20240621212758397](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621212800421-1958582678.png)





# [#](#生命周期与插件) 生命周期与插件

Maven项目构建生命周期描述的是一次构建过程经历了多少个事件

![image-20240621212937084](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621212937715-193366191.png)



Maven对项目构建的生命周期划分为3套

1. clean：清理工作
2. default：核心工作，例如编译、测试、打包、部署等
3. site：产生报告，发布站点等



clean生命周期：

- pre-clean：执行一些需要在clean之前完成的工作
- clean：移除所有上一次构建生成的文件
- post-clean：执行一些需要在clean之后立刻完成的工作



default构建生命周期：compile ——> test-compile ——> test ——> package ——> install

- validate（校验） 校验项目是否正确并且所有必要的信息可以完成项目的构建过程

- initialize（初始化） 初始化构建状态，比如设置属性值

- generate-sources（生成源代码） 生成包含在编译阶段中的任何源代码

- process-sources（处理源代码） 处理源代码，比如说，过滤任意值

- generate-resources（生成资源文件） 生成将会包含在项目包中的资源文件

- process-resources （处理资源文件） 复制和处理资源到目标目录，为打包阶段最好准备

- compile（编译） 编译项目的源代码

- process-classes（处理类文件） 处理编译生成的文件，比如说对Java class文件做字节码改善优化

- generate-test-sources（生成测试源代码） 生成包含在编译阶段中的任何测试源代码

- process-test-sources（处理测试源代码） 处理测试源代码，比如说，过滤任意值

- generate-test-resources（生成测试资源文件） 为测试创建资源文件

- process-test-resources（处理测试资源文件） 复制和处理测试资源到目标目录

- test-compile（编译测试源码） 编译测试源代码到测试目标目录

- process-test-classes（处理测试类文件） 处理测试源码编译生成的文件

- test（测试） 使用合适的单元测试框架运行测试（Juint是其中之一）

- prepare-package（准备打包） 在实际打包之前，执行任何的必要的操作为打包做准备

- package（打包） 将编译后的代码打包成可分发格式的文件，比如JAR、WAR或者EAR文件

- pre-integration-test（集成测试前） 在执行集成测试前进行必要的动作。比如说，搭建需要的环境

- integration-test（集成测试） 处理和部署项目到可以运行集成测试环境中

- post-integration-test（集成测试后） 在执行集成测试完成后进行必要的动作。比如说，清理集成测试环境

- verify （验证） 运行任意的检查来验证项目包有效且达到质量标准

- install（安装） 安装项目包到本地仓库，这样项目包可以用作其他本地项目的依赖

- deploy（部署） 将最终的项目包复制到远程仓库中与其他开发者和项目共享



site生命周期：

- pre-site 执行一些需要在生成站点文档之前完成的工作

- site 生成项目的站点文档

- post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备

- site-deploy 将生成的站点文档部署到特定的服务器上



插件：插件与生命周期内的阶段绑定，在执行到对应的生命周期时执行对应的插件功能

```xml
<build>
    <plugins>
        <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <version>2.2.1</version>
        <!--执行插件-->
        <executions>
            <!--具体怎么执行-->
            <execution>
                <!--目标：执行什么-->
                <goals>
                    <!--执行jar-->
                    <goal>jar</goal>
                </goals>
                <!--执行到那个生命周期阶段时就要执行该插件	对应前面default中的那些值-->
                <phase>generate-test-resources</phase>
            </execution>
        </executions>
        </plugin>
    </plugins>
</build>
```

`<goal>jar</goal>`值选项：[官网：Apache Maven Source Plugin](https://maven.apache.org/plugins/maven-source-plugin/)

- [source:aggregate](https://maven.apache.org/plugins/maven-source-plugin/aggregate-mojo.html) aggregrates sources for all modules in an aggregator project.
- [source:jar](https://maven.apache.org/plugins/maven-source-plugin/jar-mojo.html) is used to bundle the main sources of the project into a jar archive——对main中源代码打包
- [source:test-jar](https://maven.apache.org/plugins/maven-source-plugin/test-jar-mojo.html) on the other hand, is used to bundle the test sources of the project into a jar archive——对测试代码打包
- [source:jar-no-fork](https://maven.apache.org/plugins/maven-source-plugin/jar-no-fork-mojo.html) is similar to **jar** but does not fork the build lifecycle.
- [source:test-jar-no-fork](https://maven.apache.org/plugins/maven-source-plugin/test-jar-no-fork-mojo.html) is similar to **test-jar** but does not fork the build lifecycle.



# [#](#聚合) 聚合

作用：聚合用于快速构建Maven工程，一次性构建多个项目/模块

制作方式：创建一个空模块，打包类型定义为pom

```xml
<packaging>pom</packaging>
```

定义当前模块进行构建操作时关联的其他模块名称

```xml
<modules>
  <module>模块地址</module>
  <module>模块地址</module>
  <module>模块地址</module>
  <module>模块地址</module>
</modules>
```

注意：参与聚合操作的模块最终执行顺序与模块间的依赖关系有关，与配置顺序无关

# [#](#继承) 继承

作用：通过继承可以实现在子工程中沿用父工程中的配置（与Java类似）

制作方式：在子工程中生命其父工程坐标与对应的位置

```xml
<!--定义该工程的父工程-->
<parent>
  <groupId></groupId>
  <artifactId></artifactId>
  <version></version>
  <!--填写父工程的pom文件-->
  <relativePath>父工程pom文件地址</relativePath>
</parent>
```

在父工程中定义依赖管理

```xml
<!--声明此处进行依赖管理-->
<dependencyManagement>
  <!--具体的依赖-->
  <dependencies>
    <dependency>
      <groupId></groupId>
      <artifactId></artifactId>
      <version></version>
    </dependency>
  </dependencies>
</dependencyManagement>



<!--要管理插件的话，使用如下方式-->
<build>
	<pluginManagement>
    	<plugins>
        	<plugin>
                <groupId></groupId>
                <artifactId></artifactId>
                <version></version>
                <!--.....................-->
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

继承依赖使用：在子工程中定义依赖关系，无需声明依赖版本，版本参照父工程中依赖的版本

```xml
<!--子工程使用依赖-->
<dependencies>
  <dependency>
    <groupId></groupId>
    <artifactId></artifactId>
  </dependency>
</dependencies>


<!--子工程使用插件-->
<build>
    <plugins>
        <plugin>
            <groupId></groupId>
            <artifactId></artifactId>
            <!--<version></version>-->
        </plugin>
    </plugins>
</build>
```



继承的资源：

![image-20240622012647178](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622012650664-1657519038.png)





# [#](#继承与聚合的区别) 继承与聚合的区别

作用：聚合用于快速构建项目，继承用于快速配置

相同点：

- 聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中

- 聚合与继承均属于设计型模块，并无实际的模块内容

不同点：

- 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些

- 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己



# [#](#属性) 属性

1. 自定义属性


作用：等同于定义变量，方便统一维护

定义格式：

```xml
<!--定义自定义属性-->
<properties> 
    <spring.version>5.1.9.RELEASE</spring.version>
    <junit.version>4.12</junit.version>
</properties>
```

调用格式：

```xml
<dependency> 
    <groupId>org.springframework</groupId> 
    <artifactId>spring-context</artifactId> 
    <version>${spring.version}</version>
</dependency>
```

1. 内置属性


作用：使用Maven内置属性，快速配置

调用格式：

```xml
${basedir}
${version}
```

1. Setting属性


作用：使用Maven配置文件setting.xml中的标签属性，用于动态配置

调用格式：

```xml
${settings.localRepository}
```

1. Java系统属性


作用：读取Java系统属性

调用格式：

```xml
${user.home}
```

系统属性查询方式：

```xml
mvn help:system
```

1. 环境变量属性


作用：使用Maven配置文件setting.xml中的标签属性，用于动态配置

调用格式：

```xml
${env.JAVA_HOME}
```

环境变量属性查询方式：

```xml
mvn help:system
```



# [#](#版本管理) 版本管理

SNAPSHOT（快照版本）：

- 项目开发过程中，方便团队成员合作，解决模块间相互依赖和实时更新的问题，开发者对每个模块进行构建的时候，输出的临时性版本就叫快照版本（测试阶段版本）
- 快照版本会随着开发的进展不断更新

RELEASE（发布版本）：

- 项目开发到进入阶段里程碑后，向团队外部发布较为稳定的版本，这种版本所对应的构建文件是稳定的，即便进行功能的后续开发，也不会改变当前发布版本的内容，这种版本就叫发布版本



工程版本号约定：

<img src="https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240621205949651-1199834248.png" alt="截屏2021-07-17 下午8.24.46"  />

# [#](#资源配置) 资源配置

配置文件引用pom属性

作用：在任意配置文件中加载pom文件中定义的属性

1. pom中定义变量：

```xml
<properties>
	<jdbc.username>root</jdbc.username>
    <jdbc.password>zixieqing072413</jdbc.password>
</properties>
```

2. pom中开启配置文件加载pom属性

```xml
<build>
    <!--配置资源文件对应的信息-->
    <resources>
        <resource>
            <!--设定配置文件对应的位置目录 如 application.yml
			   支持使用属性动态设定路径	如 ${project.basedir}-->
            <directory>地址</directory>
            <!--开启对配置文件的资源加载过滤-->
            <filtering>true</filtering>
        </resource>
    </resources>
    
    
    <!--test配置文件也需要使用时：配置资源文件对应的信息-->
    <testResources>
    	<testResource>
            <!--设定配置文件对应的位置目录 如 application.yml
			   支持使用属性动态设定路径	如 ${project.basedir}-->
            <directory>地址</directory>
            <!--开启对配置文件的资源加载过滤-->
            <filtering>true</filtering>
        </testResource>
    </testResources>
</build>
```

3. 在需要使用变量得到配置文件中使用，调用格式：

```xml
${地址}


<!--示例-->
${jdbc.username}
```



# [#](#多环境开发配置) 多环境开发配置

```xml
<!--创建多环境-->
<profiles>
    <!--定义具体的环境：生产环境-->
    <profile>
        <!--定义环境对应的唯一名称	如 prod_env-->
        <id>开发环境名称1</id>
        <!--定义环境中的专用的属性值-->
        <properties>
            <jdbc.url>jdbc链接</jdbc.url>
        </properties>
        <!--将该套环境设为默认启动环境-->
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>

    <!--定义具体的环境：开发环境	格式同上-->
</profiles>
```

Maven使用命令启动那套环境：

```bash
mvn 指令 -p 环境定义id

# 示例
mvn install -p prod_env
```



# [#](#跳过测试) 跳过测试

> **提示**
>
> 正式开发时，不建议跳过

跳过测试的场景：

- 整体模块功能未开发
- 模块中某个功能未开发完毕
- 单个功能更新调试导致其他功能失败
- 快速打包
- .........................





使用方式：

1. 命令的方式

```bash
mvn 指令 –D skipTests

# 示例
mvn install –D skipTests
```

> **注意**
>
> 执行的指令生命周期必须包含测试环节



2. 使用IDEA界面操作

![image-20240622024041847](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622024043260-2133133356.png)



3. 使用pom配置

```xml
<plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.1</version>
    <configuration>
        <!-- 设置跳过测试 -->
        <skipTests>true</skipTests>
        <!-- 包含指定的测试用例 -->
        <includes>
            <include>**/User*Test.java</include>
        </includes>
        <!-- 排除指定得到测试用例 -->
        <excludes>
            <exclude>**/User*TestCase.java</exclude>
        </excludes>
    </configuration>
</plugin>
```













# [#](#私服) 私服

使用Nexus，是sonatype的产品：

- 下载地址1：https://help.sonatype.com/repomanager3/download
- 下载地址2：https://www.sonatype.com/thanks/repo-oss



启动服务器（Windows）：

```bash
nexus.exe /run nexus
```

访问服务器：默认端口8081，可在其配置文件中修改

```ba
http://localhost:8081
```

修改基础配置信息：安装路径/etc/nexus-default.properties，如默认端口号

修改服务器运行配置：安装路径/bin/nexus.vmoptions，如默认内存空间大小



## [#](#IDEA中资源上传和下载) IDEA中资源上传和下载

私服仓库分类：

宿主仓库（hosted）：保存无法从中央仓库获取的资源，如

- 自主研发
- 第三方非开源项目

代理仓库（proxy）：代理远程仓库，通过Nexus访问其他公共仓库

仓库租（group）：

- 将若干残酷组成一个群组，简化配置
- 仓库租不保存资源，属于设计型仓库



![image-20240622154257305](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622154259431-1499500529.png)



私服用户名密码：配置在本地仓库的setting.xml中即可

上传位置：配置在IDEA中，上传到私服中的仓库组不同

下载地址：配置在本地仓库的setting.xml即可





### [#](#访问私服配置) 访问私服配置

1. 配置本地仓库访问私服权限（setting.xml）

```xml
<servers>
    <server>
        <!--放release版本的	和nexus中命名保持一致-->
        <id>zixq-release</id>
        <!--访问私服的用户名密码-->
        <username>admin</username>
        <password>admin</password>
    </server>
    <server>
        <!--放snapshots版本的-->
        <id>zixq-snapshots</id>
        <username>admin</username>
        <password>admin</password>
    </server>
</servers>
```

2. 配置本地仓库来源（setting.xml）

```xml
<!--可以和阿里云仓库同时存在-->

<mirrors>
    <mirror>
    <id>nexus-zixq</id>
    <mirrorOf>*</mirrorOf>
	<url>http://localhost:8081/repository/maven-public/</url>
    </mirror>
</mirrors>
```





### [#](#IDEA发布依赖到私服配置) IDEA发布依赖到私服配置

1. 配置当前项目访问私服上传资源的保存位置（pom.xml）

```xml
<distributionManagement>
    <repository>
        <!--仓库id，和上面本地仓库setting.xml中server id保持一致-->
        <id>zixq-release</id>
        <!--放release版仓库的url-->
        <url>http://localhost:8081/repository/zixq-release/</url>
    </repository>
    <snapshotRepository>
        <id>zixq-snapshots</id>
        <url>http://localhost:8081/repository/zixq-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

2. 发布资源到私服的命令

```bash
mvn deploy
```



流程：通过IDEA pom中配置的`<url>http://localhost:8081/repository/zixq-release/</url>`访问私服，通过`<id>zixq-release</id>`去本地仓库的setting.xml 的server id找访问私服的username、password，然后进行发布





# [#](#IDEA插件) IDEA插件之Maven Helper

Maven Helper可以解决依赖问题，安装了Maven Helper插件，只要打开pom文件，就可以打开该pom文件的Dependency Analyzer视图，并且这个页面还支持搜索。很方便

进入Dependency Analyzer视图之后有三个查看选项，分别是：

- Conflicts：冲突
- All Dependencies as List：列表形式查看所有依赖
- All Dependencies as Tree：树结构查看所有依赖



![image-20240622161654202](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622161655324-269060442.png)





![image-20240622161913548](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622161914031-1846668139.png)







## [#](#jar包冲突说明) jar包冲突说明

解决IDEA中jar包冲突，使用maven的插件：maven helper插件，它能够给我们罗列出来同一个jar包的不同版本，以及它们的来源，但是对不同jar包中同名的类没有办法



### [#](#依赖顺序) 依赖顺序

点击【All Dependencies as Tree】，查看：从上向下，A依赖于B，B依赖于C

![image-20240622162333722](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622162334418-486414224.png)



.点击【Conflicts】，从图中可以看出有哪些jar存在冲突，存在冲突的情况下最终采用了哪个依赖的版本。*标红的就是冲突版本，白色的是当前的解析版本。这个选项，需要从下向上看*

![image-20240622162927630](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622162930929-1334449035.png)







### [#](#解决思路) 解决思路

1. **排除指定的版本**：如果有两个依赖的版本发生了冲突，那么只要用exclusion关键字把其中一个依赖给排除掉，只剩下一个依赖即可

![image-20240622163156783](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622163157314-1697763729.png)



2. **子工程指定版本号**

3. **子工程先排除，再指定版本号**：如下图，在starter-web中排除spring-webmvc，然后指定spring-webmvc的版本为6.0.9

![image-20240622163357642](https://img2023.cnblogs.com/blog/2421736/202406/2421736-20240622163358822-1836657012.png)



4. **锁定版本**：使用dependencyManagement统一对依赖的版本进行定义

5. **路径优先**：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高。实际使用就是直接在pom中显示使用且定义版本

6. **声明优先**：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的

7. **特殊优先**：当同级配置了相同资源的不同版本，后配置的覆盖先配置的

















