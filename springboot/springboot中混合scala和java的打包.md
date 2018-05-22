# springboot中混合scala和java开发的项目打包

>因为maven在打包项目时，不会优先打包scala文件，所以会爆出找不到java类（其实是scala文件）的错误

## 解决方法
1. 在pom.xml中的build->plugins标签下添加scala打包的插件
``` xml
 <plugin>
    <groupId>net.alchim31.maven</groupId>
    <artifactId>scala-maven-plugin</artifactId>
    <version>3.2.0</version>
    <executions>
        <execution>
            <id>compile-scala</id>
            <phase>compile</phase>
            <goals>
                <goal>add-source</goal>
                <goal>compile</goal>
            </goals>
        </execution>
        <execution>
            <id>test-compile-scala</id>
            <phase>test-compile</phase>
            <goals>
                <goal>add-source</goal>
                <goal>testCompile</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <scalaVersion>${scala.version}</scalaVersion>
    </configuration>
</plugin>
```

2. 打包命令
``` shell
mvn clean scala:compile compile package -Dmaven.test.skip=true
# 先打包scala，再打包java，跳过测试
```
