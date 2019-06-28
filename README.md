# tomcat8.5.42
tomcat8源码分析, 一点一点学习源码

##将tomcat导入idea
下载tomcat8.5.42源码包
将下载下来的apache-tomcat-8.5.42-src.zip解压，然后在解压后的apache-tomcat-8.5.42-src目录中新建catalina-home目录和pom.xml文件
apache-tomcat-8.5.42-src目录中的conf和webapps文件夹复制到catalina-home目录中
pom文件内容如下

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
        <modelVersion>4.0.0</modelVersion>
        <groupId>org.apache.tomcat</groupId>
        <artifactId>Tomcat8</artifactId>
        <name>Tomcat8</name>
        <version>8</version>
     
        <build>
            <finalName>Tomcat8.0</finalName>
            <sourceDirectory>java</sourceDirectory>
            <testSourceDirectory>test</testSourceDirectory>
            <resources>
                <resource>
                    <directory>java</directory>
                </resource>
            </resources>
            <testResources>
               <testResource>
                    <directory>test</directory>
               </testResource>
            </testResources>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <encoding>UTF-8</encoding>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
     
        <dependencies>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.easymock</groupId>
                <artifactId>easymock</artifactId>
                <version>3.4</version>
            </dependency>
            <dependency>
                <groupId>ant</groupId>
                <artifactId>ant</artifactId>
                <version>1.7.0</version>
            </dependency>
            <dependency>
                <groupId>wsdl4j</groupId>
                <artifactId>wsdl4j</artifactId>
                <version>1.6.2</version>
            </dependency>
            <dependency>
                <groupId>javax.xml</groupId>
                <artifactId>jaxrpc</artifactId>
                <version>1.1</version>
            </dependency>
            <dependency>
                <groupId>org.eclipse.jdt.core.compiler</groupId>
                <artifactId>ecj</artifactId>
                <version>4.5.1</version>
            </dependency>
            <dependency>
                <groupId>javax.xml.soap</groupId>
                <artifactId>javax.xml.soap-api</artifactId>
                <version>1.4.0</version>
            </dependency>
    
        </dependencies>
    </project>

catalina-home目录结构如下

conf\
lib\
logs\
temp\
webapps\
work

lib文件夹是空的\
至此基本的准备工作已经完成了

将apache-tomcat-8.5.42-src项目导入进IDEA中\
配置启动参数

Add New Configuration添加Application

Main Class :  org.apache.catalina.startup.Bootstrap
VM options :  -Dcatalina.home=H:\workspace\code\tomcat\tomcat8.5.42\catalina-home -Dcatalina.base=H:\workspace\code\tomcat\tomcat8.5.42\catalina-home -Djava.endorsed.dirs=H:\workspace\code\tomcat\tomcat8.5.42\catalina-home\endorsed -Djava.io.tmpdir=H:\workspace\code\tomcat\tomcat8.5.42\catalina-home\temp -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=H:\workspace\code\tomcat\tomcat8.5.42\catalina-home\conf\logging.properties

说明：如果编译build的时候出现Test测试代码报错，注释该代码即可。本文中的Tomcat源码util.TestCookieFilter类会报错，将其注释即可。
上面第三步已经构建了项目的运行环境，点击运行或者调试按钮后，正常运行。
项目启动完毕我们可以测试了，在浏览器访问http://localhost:8080/  发现打不开我们看到的经典欢迎页面了，页面报了一个错
原因是我们直接启动org.apache.catalina.startup.Bootstrap的时候没有加载org.apache.jasper.servlet.JasperInitializer，从而无法编译JSP。解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中手动将JSP解析器初始化：
大约在774行的webConfig();后面加上

context.addServletContainerInitializer(new JasperInitializer(), null);

重新启动,应该就能看见熟悉的tomcat页面了


