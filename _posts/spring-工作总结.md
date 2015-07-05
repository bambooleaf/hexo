title: "spring 工作总结"
date: 2015-05-18 21:50:21
categories: 工作总结
tags: spring
---

公司另一个项目组换框架，将要换成spring的框架，正好我之前做的项目用的spring，但是我本身也没有理解的多透彻。明天需要我去简单培训一下，所以我就借此机会好好复习一下了

#spring核心思想
## IOC（控制反转）
  Spring容器来实现这些相互依赖对象的创建、协调工作。对象只需要关系业务逻辑本身就可以了。从这方面来说，对象如何得到他的协作对象的责任被反转了（IOC、DI）。
<!--more-->
  所谓IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。
  那么DI是如何实现的呢？ Java 1.3之后一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。
  具体文章解释，我觉得这篇写的很好，有时间要好好再看一看http://blog.csdn.net/it_man/article/details/4402245
## AOP（面向切面编程）
面向切面编程（也叫面向方面编程），是目前软件开发中的一个热点，也是Spring框架中的一个重要内容。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
主要包括以下几个方面

 1. 切面Aspect
 2. 连接点Joinpoint
 3. 通知Advice
 4. 切入点Pointcut
 5. 目标对象Target Object
 6. AOP代理AOP Proxy
 
具体可参考http://blog.csdn.net/wangpeng047/article/details/8556800
也可参考http://blog.csdn.net/moreevan/article/details/11977115

#java注解
使用spring最大的感受就是大量的注解，很方便，但是java基础不怎么样的我就要好好复习一下注解的知识了
##何为注解
 说起注解，得先提一提什么是元数据(metadata)。所谓元数据就是数据的数据。也就是说，元数据是描述数据的。就象数据表中的字段一样，每个字段描述了这个字段下的数据的含义。而J2SE5.0中提供的注解就是java源代码的元数据，也就是说注解是描述java源代码的。在J2SE5.0中可以自定义注解。使用时在@后面跟注解的名字。
 

 
##预定义的注解（Override、Deprecated和SuppressWarnings）
 
###Override
这个注解的作用是标识某一个方法是否覆盖了它的父类的方法，在eclipse下开发，当你继承了一个类或接口，开发工具会自动添加出这个东西。它的主要作用就是防止继承方法名字写错，这样是不会报错的，只会认为你添加了子类的新方法而已，结果就是没有覆盖父类方法，而且编译也不会报错。
 
###Deprecated
这个注解是一个标记注解。所谓标记注解，就是在源程序中加入这个标记后，并不影响程序的编译，但有时编译器会显示一些警告信息。

那么Deprecated注解是什么意思呢？如果你经常使用eclipse等IDE编写java程序时，可能会经常在属性或方法提示中看到这个词。如果某个类成员的提示中出现了个词，就表示这个并不建议使用这个类成员。因为这个类成员在未来的JDK版本中可能被删除。之所以在现在还保留，是因为给那些已经使用了这些类成员的程序一个缓冲期。如果现在就去了，那么这些程序就无法在新的编译器中编译了。
 
###SuppressWarnings
这个世界的事物总是成对出现。即然有使编译器产生警告信息的，那么就有抑制编译器产生警告信息的。

SuppressWarnings注解就是为了这样一个目的而存在的。

###自定义注解

 - 元注解
 注解可以用于注解类（annotate Classes）
可以用于注解接口(annotate Interfaces)
可以用于注解枚举类型(annotate Enums)
因此注解同样也可以用于注解注解（annotate Annotations）
它们是Target、Retention、Documented和Inherited。
 - 注解的本质：注解是一种类型


具体可参考http://blog.csdn.net/rj042/article/details/6399965

#spring 注解
这个部分我始终只是能做到使用，但是具体原理始终没有理解透彻，这里先记下个地址留着以后回头来重新理解http://blog.csdn.net/hjm4702192/article/details/9420297?utm_source=tuicool

 

