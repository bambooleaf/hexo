title: "hibernate 工作总结"
date: 2015-05-19 21:57:02
categories: 工作总结
tags: hibernate
---


#hibernate核心思想
整体理解可参考[hibernate整体理解][1]
hibernate是一个采用ORM（Object/Relation Mapping对象关系映射）机制持久层的开源框架
    其主要核心思想是面向对象，而非面向过程，而这个面向对象则主要通过ORM实现。
    ORM是将表与表之间的操作，映射成对象和对象之间的操作，就是通过操作实体类来达到操作表的目的。从数据库提取的数据会自动按你设置的映射要求封装成特定的对象。之后你就可以通过对对象进行操作来修改数据库中的数据。**这时候你面对的不是信息的碎片，而是一个形象鲜明的对象**。
<!--more-->
    hibernate一对一映射等映射可参考[实体类之间映射][2]
    关于主要的主键生成策略，可参考[《Hibernate主键生成策略总结》][3]
#Hibernate Session & Transaction详解  
##hibernate 的 session
Session是JAVA应用程序和Hibernate进行交互时使用的主要接口，它也是持久化操作核心API， 
　　注意这里的Session的含义，它与传统意思上web层的HttpSession并没有关系，Hibernate Session之与Hibernate，相当于JDBC Connection相对与JDBC。 
　　Session对象是有生命周期的，它以Transaction对象的事务开始和结束边界 
　　Session作为贯穿Hibernate的持久化管理器核心，提供了众多的持久化的方法，如save(), update ,delete ,find(Hibernate 3中已经取消了此方法)等，通过这些方法我们可以透明的完成对象的增删改查（CRUD-- create read update delete），这里所谓的透明是指，Session在读取，创建和删除影射的实体对象的实例时，这一系列的操作将被转换为对数据库表中数据的增加，修改，查询和删除操作。

SessionFactory负责创建Session，SessionFactory是线程安全的，多个并发线程可以同时访问一个SessionFactory 并从中获取Session实例。而Session并非线程安全，也就是说，如果多个线程同时使用一个Session实例进行数据存取，则将会导致Session 数据存取逻辑混乱.因此创建的Session实例必须在本地存取空上运行，使之总与当前的线程相关。


Session有以下的特点
　　1,不是线程安全的，应该避免多个线程共享同一个Session实例 
　　2,Session实例是轻量级的，所谓轻量级：是指他的创建和删除不需要消耗太多资源 
　　3,Session对象内部有一个缓存，被称为Hibernate第一缓存，他存放被当前工作单元中加载的对象，每个Session实例都有自己的缓存。
　　
Hibernate Session缓存被称为Hibernate的第一级缓存。SessionFactory的外置缓存称为Hibernate的二级缓存。这两个缓存都位于持久层，它们存放的都是数据库数据的拷贝。SessionFactory的内置缓存 存放元数据和预定义SQL， SessionFactory的内置缓存是只读缓存。

Hibernate Session缓存的三大作用：
1，减少数据库的访问频率，提高访问性能。
2，保证缓存中的对象与数据库同步，位于缓存中的对象称为持久化对象。
3，当持久化对象之间存在关联时，Session 保证不出现对象图的死锁。
Session 如何判断持久化对象的状态的改变呢？
Session 加载对象后会为对象值类型的属性复制一份快照。当Session 清理缓存时，比较当前对象和它的快照就可以知道那些属性发生了变化。

Session 什么时候清理缓存？
1，commit（） 方法被调用时
2，查询时会清理缓存，保证查询结果能反映对象的最新状态。
3，显示的调用session 的 flush方法。
session 清理缓存的特例：
当对象使用 native 生成器 时 会立刻清理缓存向数据库中插入记录。

##hibernate的Transanction
Transanction接口是Hibernate的数据库事务接口，用于管理事务，他对底层的事务作出了封装，用户可以使用Transanction对象定义自己的对数据库的原子操作，底层事务包括：JDBC API ,JTA(Java Transaction API)。。。。。 
　　一个Transaction对象的事务可能会包括多个对数据库进行的操作 
　　org.hibernate Interface Transaction 
　　public interface Transaction
常用方法：
　　public void commit() throws HibernateException 刷新当前的Session以及结束事务的工作，这个方法将迫使数据库对当前的事务进行提交 
　　public void rollback() throws HibernateException ：强迫回滚当前事务 
　　public boolean isActive() throws HibernateException： 这个事务是否存活 
**在不同环境下transaction略有不同**
1、在JDBC上使用Hibernate 

必须写上Hibernate Transaction代码，否则数据库没有反应。此时Hibernate的Transaction就是Connection.commit而已 

2、在JTA上使用Hibernate 

写JTA的Transaction代码，不要写Hibernate的Transaction代码，否则程序会报错 

3、在EJB上使用Hibernate 

什么Transactioin代码都不要写，在EJB的部署描述符里面配置 

参考：http://blog.163.com/magicc_love/blog/static/1858536622012092478227/

#spring线程安全问题
Spring框架里的bean，或者说组件，获取实例的时候都是默认的单例模式，这是在多线程开发的时候要尤其注意的地方。那么spring是如何保证线程安全的呢？
**通过TheadLocal类**
具体内容可参考下面两篇文章
[Spring单实例、多线程安全、事务解析][4]
[Spring单例与线程安全小结][5]



#配置多数据源
有人提到了这个问题，不过我没有实践过，所以就先在这里记下个解决方案，以后有机会去试试吧[Spring3+Hibernate3(Jpa)配置多个数据源的解决方案（基于注解）][6]


  [1]: http://blog.csdn.net/wanghuan203/article/details/7526246
  [2]: http://blog.csdn.net/wanghuan203/article/details/7566518
  [3]: http://blog.csdn.net/wanghuan203/article/details/7562395
  [4]: http://blog.csdn.net/c289054531/article/details/9196053
  [5]: http://www.cnblogs.com/doit8791/p/4093808.html
  [6]: http://blog.csdn.net/linxuliang/article/details/7838132#comments
