# springboot 项目中使用了websocket影响springboot test的问题解决

## 原因
在springboot的test中，一个SpringApplication将尝试创建正确类型的 ApplicationContext 。默认情况下，在JUnit测试中使用SpringApplication 时会调用setWebEnvironment(false) 。
而我们再项目中使用了websocket时，JUnit测试类中测试services时应该是web的测试环境，所以在运行测试类时报错，而注释掉websocket的配置时，不会报错。

## 解决
根据以下代码修改测试类的环境注解，改为webapp的测试环境即可
``` java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
```
