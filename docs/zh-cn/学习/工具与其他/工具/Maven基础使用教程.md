#	1. Maven概念

Maven 是一个项目管理工具，可以对 Java 项目进行构建、依赖管理。

可以通过一小段描述信息来管理**项目的构建**，报告和文档的**项目管理工具**软件。

可以实现jar包的管理，构建工程目录，管理jar包、发布，运行等......管理项目使用





#	2.Maven安装

1. 解压文件夹到磁盘，比如：--D:\apache-maven-3.6.2

2. 设置Maven的环境变量，基本上和设置Jdk环境变量相似，首先设置MAVEN_HOME，再加入Path 。

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130210243.png)

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130210311.png)

3. 在D盘根创建一个文件夹：D:\maven_rep

4. 修改setting文件：D:\apache-maven-3.6.2\conf\Setting

   修改本地仓库位置：默认： ${user.home}/.m2/repository

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130210545.png)

   修改拉取服务器地址：默认是官网，比较慢，配置成阿里的镜像，下载比较快

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130210752.png)

   ```xml
   <mirror>
       <id>aliyun-maven</id>
       <mirrorOf>central</mirrorOf>
       <name>aliyun maven mirror</name>
       <url>https://maven.aliyun.com/repository/central</url>
   </mirror>
   ```





# 3.常用命令

mvn archetype:generate 创建Maven项目

<font style="color:red">mvn compile 编译源代码</font>

mvn deploy 发布项目

mvn test-compile 编译测试源代码

mvn test 运行应用程序中的单元测试

mvn site 生成项目相关信息的网站

<font style="color:red">mvn clean 清除项目目录中的生成结果</font>

<font style="color:red">mvn package 根据项目生成的jar</font>

<font style="color:red">mvn install 在本地Repository中安装jar</font>

mvn eclipse:eclipse 生成eclipse项目文件

mvnjetty:run 启动jetty服务

mvntomcat:run 启动tomcat服务

<font style="color:red">mvn clean package -Dmaven.test.skip=true:清除以前的包后重新打包，跳过测试类</font>





#	4.构建工程

命令格式如下：

```
mvn archetype:generate -DgroupId=com.companyname.bank
 -DartifactId=consumerBanking
-DarchetypeArtifactId=maven-archetype-quicksart -DinteractiveMode=false
```



参数说明：

·     **-DgourpId**: 组织名，公司网址的反写 + 项目名称

·     **-DartifactId**: 项目名-模块名

·     **-DarchetypeArtifactId**: 指定 ArchetypeId，maven-archetype-quickstart，创建一个简单的 Java 应用

·     **-DinteractiveMode**: 是否使用交互模式





#	5. uMaven 构建生命周期

Maven 构建生命周期定义了一个项目构建跟发布的过程。

一个典型的 Maven 构建（build）生命周期是由以下几个阶段的序列组成的：

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130211403.png)

| 阶段          | 处理     | 描述                                                     |
| :------------ | :------- | :------------------------------------------------------- |
| 验证 validate | 验证项目 | 验证项目是否正确且所有必须信息是可用的                   |
| 编译 compile  | 执行编译 | 源代码编译在此阶段完成                                   |
| 测试 Test     | 测试     | 使用适当的单元测试框架（例如JUnit）运行测试。            |
| 包装 package  | 打包     | 创建JAR/WAR包如在 pom.xml 中定义提及的包                 |
| 检查 verify   | 检查     | 对集成测试的结果进行检查，以保证质量达标                 |
| 安装 install  | 安装     | 安装打包的项目到本地仓库，以供其他项目使用               |
| 部署 deploy   | 部署     | 拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程 |





#	6.Maven插件

创建 jar 文件、创建 war 文件、编译代码文件、代码单元测试、创建工程文档、创建工程报告

Maven 有以下三个标准的生命周期：

- **clean**：项目清理的处理
- **default(或 build)**：项目部署的处理
- **site**：项目站点文档创建的处理

Maven 实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成。Maven 插件通常被用来：

- 创建 jar 文件
- 创建 war 文件
- 编译代码文件
- 代码单元测试
- 创建工程文档
- 创建工程报告

| 插件     | 描述                                                |
| :------- | :-------------------------------------------------- |
| clean    | 构建之后清理目标文件。删除目标目录。                |
| compiler | 编译 Java 源文件。                                  |
| surefile | 运行 JUnit 单元测试。创建测试报告。                 |
| jar      | 从当前工程中构建 JAR 文件。                         |
| war      | 从当前工程中构建 WAR 文件。                         |
| javadoc  | 为工程生成 Javadoc。                                |
| antrun   | 从构建过程的任意一个阶段中运行一个 ant 任务的集合。 |

插件是在 pom.xml 中使用 plugins 元素定义的。





# 7.Idea的maven配置

关于 IntelliJ IDEA 的一些特性如下：

可以通过 IntelliJ IDEA 来运行 Maven 目标；可以在 IntelliJ IDEA 自己的终端里查看 Maven 命令的输出结果；可以在 IDE 里更新 Maven 的依赖关系；可以在 IntelliJ IDEA 中启动 Maven 的构建；IntelliJ IDEA 基于 Maven 的 pom.xml 来实现自动化管理依赖关系；IntelliJ IDEA 可以自动从远程 Moven 仓库上下载需要的依赖和源码等

1. 打开setting
2. 搜索maven配置

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130212144.png)





#	8.Idea的maven项目创建

1. file—new project

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130212412.png)

2. 选择maven环境：

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130212506.png)

   

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130212533.png)

   ​	进入之后：点击enable-auto-import

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200130212628.png)

   

