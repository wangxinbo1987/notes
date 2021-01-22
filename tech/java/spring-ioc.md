### IoC - Inversion of Control

> 如果一个系统有大量的组件，其生命周期和相互之间的依赖关系如果由组件自身来维护，不但大大增加了系统的复杂度，而且会导致组件之间极为紧密的耦合，继而给测试和维护带来了极大的困难。

**IoC**解决了组件的**生命周期管理**问题，例如

- 谁负责创建组件？
- 谁负责根据依赖关系组装组件？
- 销毁时，如何按依赖顺序正确销毁？

在IoC模式下，控制权发生了反转，即从应用程序转移到了IoC容器，所有组件不再由应用程序自己创建和配置，而是由IoC容器负责，这样，应用程序只需要直接使用已经创建好并且配置好的组件

Spring的IoC容器接口为`ApplicationContext`，有多种实现类，常用的有`ClassPathXmlApplicationContext`（从xml文件读取bean装配信息），`AnnotationConfigApplicationContext`（从注解读取bean装配信息）等

通过注解方式配置bean更为灵活，常用的注解有

---

`@Component`：定义bean，默认名字是小写字母开头的类名，例如

```java
@Component
public class MailService {
    ...
}
```

> `@Component`默认创建的是单例，如果需要每次注入新的示例，需要加上`@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)`注解

---

`@Autowired`：注入bean，可以加在字段set()方法，字段本身，或构造方法，常用的为加在字段本身，例如

```java
public class UserService {

    @Autowired
    MailService mailService;
    
    ...
}
```

> `@Autowired`也可以加在`List`对象上，此时容器会将所有符合条件的bean注入该对象，可以在`@Component`注解时追加`@Order(1)`注解来指定顺序

> `@Autowired(required = false)`表示非必须存在bean定义，找不到就不注入；默认`required = true`，如果没有找到符合条件的bean会抛出`NoSuchBeanDefinitionException`异常

---

`@Configuration`：配置类，例如

```java
@Configuration
@ComponentScan
public class AppConfig {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        
        ...
    }
}
```

上例中`@ComponentScan`用于告诉容器自动搜索当前类所在的包以及子包，创建通过`@Component`注解的bean，并通过`@Autowired`注入

未完待续。。。