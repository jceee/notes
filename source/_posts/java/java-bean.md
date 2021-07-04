title: java学习笔记[bean注解]
date: 2018-03-30 22:22:53
tags: java
categories: java
---

>java的注解可以实现很多功能，如类的声明，属性声明，注入声明，配置...等等，这里对其作用做了笔记

# Bean的声明

* @Component 通用，无明确角色
* @Repository. 数据访问层（Dao）层注解
* @Controller  Controller层
* @Service  业务逻辑Service层
* @Configuration 配置文件

# Scope

## 作用域类型

@Scope(“single”)    
* single          每个IOC容器对一个bean定义创建唯一实例 
* prototype    对一个bean定义，每次请求容器都会创建新的实例 
* session         对一个bean定义，一次web会话创建一个实例 
* request            对一个bean定义，一次web请求会创建一个实例 
* global session    对一个bean定义，一次porlet会话创建一个实例 

## 代理
scoped-proxy的意义在关联bean之间的依赖
proxy，它代理的工作就是——暴露这个bean时令其符合其作用域的特性。

```
 @Scope(proxyMode = ScopedProxyMode.TARGET_CLASS)
```
 

能够在HTTP request或者Session（甚至自定义）作用域中定义bean固然很好，但是Spring IoC容器除了管理对象（bean）的实例化，同时还负责协作者（或者叫依赖）的实例化。
如果你打算将一个Http request范围的bean注入到另一个bean中，那么需要注入一个AOP代理来替代被注入的作用域bean。
也就是说，你需要注入一个代理对象，该对象具有与被代理对象一样的公共接口，而容器则可以足够智能的从相关作用域中（比如一个HTTP request）获取到真实的目标对象，并把方法调用委派给实际的对象。


proxyMode：代理方式

```
取值说明：  
ScopedProxyMode.DEFAULT:           默认  
ScopedProxyMode.NO:                不使用代理
ScopedProxyMode.INTERFACES:        接口    ---jdk动态代理
ScopedProxyMode.TARGET_CLASS:      类      ---cglib
```

# Configuration

@Bean
配置Bean

# Bean的注入

* @Autowired          Spring 提供的注解 .    (required=false)非必要注入，找不到不会报错
* @Resource           JSR-250提供的注解.    
* @Inject                JSR-330提供的注解

## 区别

‘@Autowired’和‘@Inject’这两个注解行为完全相同，都是通过AutowiredAnnotation的BeanPostProcessor来实现依赖注入。也就是说，在注入Spring bean的时候‘Autowired’和‘@Inject’可以互换。

而‘@Resource’使用CommonAnnotationBeanPostProcessor来实现注入。虽然它使用的注入处理类不一样，但是其实所有这三个注解行为几乎一样。

下面是它们执行逻辑顺序的总结：

`@Autowired和@Inject`

按照类型匹配
使用限定符进行类型限定
按照名称匹配


`@Resource`


按照名称匹配
按照类型匹配
使用限定符进行类型限定（但如果名称匹配成功的话这条会被忽略）


Ps: 使用@Inject需要Jsr-303的支持，添加依赖

```
		<dependency>
			<groupId>javax.inject</groupId>
			<artifactId>javax.inject</artifactId>
			<version>1</version>
		</dependency>
```

# List,Map的注入

```
class Example{
    @Autowired
		private List<Bean> list;
}
```

## Qualifier

限定符

# Required

@Required注解适用于bean属性的setter方法并且它指示，受影响的bean属性必须在配置时被填充在XML配置文件中，否则容器将抛出BeanInitializationException例外。

```
class Demo{
    private String name;

    @Required
    public void setName(String name){
        this.name = name;
    }
}

Class Demo2 {
    @Autowired(value="name")
    private Demo demo;
}
```


# Meta-annotations元注解

* @Retention：

 可以设置三种值RetentionPolicy.CLASS、RentionPolicy.RUNTIME、RentionPolicy.SOURCE，表明描述注解的生命周期，即注解的生效范围，分别是：
   1.SOURCE：在源文件中生效，仅存在java文件中，class文件将会去除注解。

   2.CLASS：在class文件中生效，仅保留在class文件中，运行时无法获取注解。

   3.RUNTIME:在运行时生效，保留在class文件中且运行时可通过反射机制获取。

* @Target：

 表示该新定义Annotation的作用目标，具体可参考API文档。
    1.CONSTRUCTOR:用于描述构造器

    2.FIELD:用于描述域（成员变量）

    3.LOCAL_VARIABLE:用于描述局部变量

    4.METHOD:用于描述方法

    5.PACKAGE:用于描述包

    6.PARAMETER:用于描述参数

    7.TYPE:用于描述类、接口(包括注解类型) 或enum声明

* @Documented

 表示该新定义Annotation是否保留在Java Docs中。

* @Inherited

 表示使用该新定义Annotation可以继承。



