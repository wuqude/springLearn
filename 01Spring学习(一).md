## 1，课程介绍

对于一门新技术，我们需要从`为什么要学`、`学什么`以及`怎么学`这三个方向入手来学习。那对于Spring来说:

### 1.1 为什么要学?

* 从使用和占有率看

  * Spring在市场的占有率与使用率高

  * Spring在企业的技术选型命中率高

  * 所以说,Spring技术是JavaEE开发必备技能，企业开发技术选型命中率>==90%==

    ![image-20210729171139088](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729171139088.png)

    **说明**:对于未使用Spring的项目一般都是些比较老的项目，大多都处于维护阶段。

* 从专业角度看

  * 随着时代发展，软件规模与功能都呈几何式增长，开发难度也在不断递增，该如何解决?
    * Spring可以==简化开发==，降低企业级开发的复杂性，使开发变得更简单快捷
  * 随着项目规模与功能的增长,遇到的问题就会增多，为了解决问题会引入更多的框架，这些框架如何协调工作?
    * Spring可以==框架整合==，高效整合其他技术，提高企业级应用开发与运行效率

  综上所述，==Spring是一款非常优秀而且功能强大的框架，不仅要学，而且还要学好。==

### 1.2 学什么?

从上面的介绍中，我们可以看到Spring框架主要的优势是在`简化开发`和`框架整合`上，至于如何实现就是咱们要学习Spring框架的主要内容:

* 简化开发: Spring框架中提供了两个大的核心技术，分别是:

  * ==IOC==
  * ==AOP==
    * ==事务处理==

   1.Spring的简化操作都是基于这两块内容,所以这也是Spring学习中最为重要的两个知识点。

   2.事务处理属于Spring中AOP的具体应用，可以简化项目中的事务管理，也是Spring技术中的一大亮点。

* 框架整合: Spring在框架整合这块已经做到了极致，它可以整合市面上几乎所有主流框架，比如:

  - ==MyBatis==
  - MyBatis-plus
  - Struts
  - Struts2
  - Hibernate
  - ……

  这些框架中，我们目前只学习了MyBatis，所以在Spring框架的学习中，主要是学习如何整合MyBatis。

  综上所述，对于Spring的学习，主要学习四块内容:

  ==(1)IOC,(2)整合Mybatis(IOC的具体应用)，(3)AOP,(4)声明式事务(AOP的具体应用)==

### 1.3 怎么学?

* 学习Spring框架设计思想
  * 对于Spring来说，它能迅速占领全球市场，不只是说它的某个功能比较强大，更重要是在它的`思想`上。
* 学习基础操作，思考操作与思想间的联系
  * 掌握了Spring的设计思想，然后就需要通过一些基础操作来思考操作与思想之间的关联关系
* 学习案例，熟练应用操作的同时，体会思想
  * 会了基础操作后，就需要通过大量案例来熟练掌握框架的具体应用，加深对设计思想的理解。

## 2，Spring相关概念

### 2.1 初识Spring

在这一节，主要通过以下两个点来了解下Spring:

#### 2.1.1 Spring家族

* 官网：https://spring.io，从官网我们可以大概了解到：

  * Spring能做什么:用以开发web、微服务以及分布式系统等,光这三块就已经占了JavaEE开发的九成多。
  * Spring并不是单一的一个技术，而是一个大家族，可以从官网的`Projects`中查看其包含的所有技术。

* Spring发展到今天已经形成了一种开发的生态圈,Spring提供了若干个项目,每个项目用于完成特定的功能。

  * Spring已形成了完整的生态圈，也就是说我们可以完全使用Spring技术完成整个项目的构建、设计与开发。

  * Spring有若干个项目，可以根据需要自行选择，把这些个项目组合起来，起了一个名称叫==全家桶==，如下图所示

    ![image-20210729171850181](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729171850181.png)

    **说明:**

    图中的图标都代表什么含义，可以进入`https://spring.io/projects`网站进行对比查看。

    这些技术并不是所有的都需要学习，额外需要重点关注`Spring Framework`、`SpringBoot`和`SpringCloud`:

    ![1629714811435](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629714811435.png)

    * Spring Framework:Spring框架，是Spring中最早最核心的技术，也是所有其他技术的基础。
    * SpringBoot:Spring是来简化开发，而SpringBoot是来帮助Spring在简化的基础上能更快速进行开发。
    * SpringCloud:这个是用来做分布式之微服务架构的相关开发。

    除了上面的这三个技术外，还有很多其他的技术，也比较流行，如SpringData,SpringSecurity等，这些都可以被应用在我们的项目中。我们今天所学习的Spring其实指的是==Spring Framework==。

#### 2.1.2 了解Spring发展史

 接下来我们介绍下Spring Framework这个技术是如何来的呢?

![image-20210729171926576](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729171926576.png)

Spring发展史

* IBM(IT公司-国际商业机器公司)在1997年提出了EJB思想,早期的JAVAEE开发大都基于该思想。
* Rod Johnson(Java和J2EE开发领域的专家)在2002年出版的`Expert One-on-One J2EE Design and Development`,书中有阐述在开发中使用EJB该如何做。
* Rod Johnson在2004年出版的`Expert One-on-One J2EE Development without EJB`,书中提出了比EJB思想更高效的实现方案，并且在同年将方案进行了具体的落地实现，这个实现就是Spring1.0。
* 随着时间推移，版本不断更新维护，目前最新的是Spring5
  * Spring1.0是纯配置文件开发
  * Spring2.0为了简化开发引入了注解开发，此时是配置文件加注解的开发方式
  * Spring3.0已经可以进行纯注解开发，使开发效率大幅提升，我们的课程会以注解开发为主
  * Spring4.0根据JDK的版本升级对个别API进行了调整
  * Spring5.0已经全面支持JDK8，现在Spring最新的是5系列所以建议大家把JDK安装成1.8版

本节介绍了Spring家族与Spring的发展史，需要大家重点掌握的是:

* 今天所学的Spring其实是Spring家族中的Spring Framework
* Spring Framework是Spring家族中其他框架的底层基础，学好Spring可以为其他Spring框架的学习打好基础

### 2.2 Spring系统架构

前面我们说spring指的是Spring Framework,那么它其中都包含哪些内容以及我们该如何学习这个框架?

针对这些问题，我们将从`系统架构图`和`课程学习路线`来进行说明:

#### 2.2.1 系统架构图

* Spring Framework是Spring生态圈中最基础的项目，是其他项目的根基。

* Spring Framework的发展也经历了很多版本的变更，每个版本都有相应的调整

  ![image-20210729172153796](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729172153796.png)

* Spring Framework的5版本目前没有最新的架构图，而最新的是4版本，所以接下来主要研究的是4的架构图

  ![1629720945720](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629720945720.png)

  (1)核心层

  * Core Container:核心容器，这个模块是Spring最核心的模块，其他的都需要依赖该模块

  (2)AOP层

  * AOP:面向切面编程，它依赖核心层容器，目的是==在不改变原有代码的前提下对其进行功能增强==
  * Aspects:AOP是思想,Aspects是对AOP思想的具体实现

  (3)数据层

  * Data Access:数据访问，Spring全家桶中有对数据访问的具体实现技术
  * Data Integration:数据集成，Spring支持整合其他的数据层解决方案，比如Mybatis
  * Transactions:事务，Spring中事务管理是Spring AOP的一个具体实现，也是后期学习的重点内容

  (4)Web层

  * 这一层的内容将在SpringMVC框架具体学习

  (5)Test层

  * Spring主要整合了Junit来完成单元测试和集成测试

#### 2.2.2 课程学习路线

介绍完Spring的体系结构后，从中我们可以得出对于Spring的学习主要包含四部分内容，分别是:

* ==Spring的IOC/DI==
* ==Spring的AOP==
* ==AOP的具体应用,事务管理==
* ==IOC/DI的具体应用,整合Mybatis==

![image-20220919141110917](C:\Users\Yilin.Yang\AppData\Roaming\Typora\typora-user-images\image-20220919141110917.png)

对于这节的内容，大家重点要记住的是Spring需要学习的四部分内容。接下来就从第一部分开始学起。

### 2.3 Spring核心概念

在Spring核心概念这部分内容中主要包含`IOC/DI`、`IOC容器`和`Bean`,那么问题就来了，这些都是什么呢?

#### 2.3.1 目前项目中的问题

要想解答这个问题，就需要先分析下目前咱们代码在编写过程中遇到的问题:

![image-20220919141333806](C:\Users\Yilin.Yang\AppData\Roaming\Typora\typora-user-images\image-20220919141333806.png)

(1)业务层需要调用数据层的方法，就需要在业务层new数据层的对象

(2)如果数据层的实现类发生变化，那么业务层的代码也需要跟着改变，发生变更后，都需要进行编译打包和重部署

(3)所以，现在代码在编写的过程中存在的问题是：==耦合度偏高==

针对这个问题，该如何解决呢?

![image-20220919141647587](C:\Users\Yilin.Yang\AppData\Roaming\Typora\typora-user-images\image-20220919141647587.png)

我们就想，如果能把框中的内容给去掉，不就可以降低依赖了么，但是又会引入新的问题，去掉以后程序能运行么?

答案肯定是不行，因为bookDao没有赋值为Null，强行运行就会出空指针异常。

所以现在的问题就是，业务层不想new对象，运行的时候又需要这个对象，该咋办呢?

针对这个问题，Spring就提出了一个解决方案:

* 使用对象时，在程序中不要主动使用new产生对象，转换为由==外部==提供对象

这种实现思就是Spring的一个核心概念

#### 2.3.2 IOC、IOC容器、Bean、DI

1. ==IOC（Inversion of Control）控制反转==

(1)什么是控制反转呢？

* 使用对象时，由主动new产生对象转换为由==外部==提供对象，此过程中对象创建控制权由程序转移到外部，此思想称为控制反转。
  * 业务层要用数据层的类对象，以前是自己`new`的
  * 现在自己不new了，交给`别人[外部]`来创建对象
  * `别人[外部]`就反转控制了数据层对象的创建权
  * 这种思想就是控制反转
  * 别人[外部]指定是什么呢?继续往下学

(2)Spring和IOC之间的关系是什么呢?

* Spring技术对IOC思想进行了实现
* Spring提供了一个容器，称为==IOC容器==，用来充当IOC思想中的"外部"
* IOC思想中的`别人[外部]`指的就是Spring的IOC容器

(3)IOC容器的作用以及内部存放的是什么?

* IOC容器负责对象的创建、初始化等一系列工作，其中包含了数据层和业务层的类对象
* 被创建或被管理的对象在IOC容器中统称为==Bean==
* IOC容器中放的就是一个个的Bean对象

(4)当IOC容器中创建好service和dao对象后，程序能正确执行么?

* 不行，因为service运行需要依赖dao对象
* IOC容器中虽然有service和dao对象
* 但是service对象和dao对象没有任何关系
* 需要把dao对象交给service,也就是说要绑定service和dao对象之间的关系

像这种在容器中建立对象与对象之间的绑定关系就要用到DI:

2. ==DI（Dependency Injection）依赖注入==

![image-20220919150351884](C:\Users\Yilin.Yang\AppData\Roaming\Typora\typora-user-images\image-20220919150351884.png)

(1)什么是依赖注入呢?

* 在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入
  * 业务层要用数据层的类对象，以前是自己`new`的
  * 现在自己不new了，靠`别人[外部其实指的就是IOC容器]`来给注入进来
  * 这种思想就是依赖注入

(2)IOC容器中哪些bean之间要建立依赖关系呢?

* 这个需要程序员根据业务需求提前建立好关系，如业务层需要依赖数据层，service就要和dao建立依赖关系

介绍完Spring的IOC和DI的概念后，我们会发现这两个概念的最终目标就是:==充分解耦==，具体实现靠:

* 使用IOC容器管理bean（IOC)
* 在IOC容器内将有依赖关系的bean进行关系绑定（DI）
* 最终结果为:使用对象时不仅可以直接从IOC容器中获取，并且获取到的bean已经绑定了所有的依赖关系.

#### 2.3.3 核心概念小结

这节比较重要，重点要理解`什么是IOC/DI思想`、`什么是IOC容器`和`什么是Bean`：

(1)什么IOC/DI思想?

* IOC:控制反转，控制反转的是对象的创建权
* DI:依赖注入，绑定对象与对象之间的依赖关系

(2)什么是IOC容器?

Spring创建了一个容器用来存放所创建的对象，这个容器就叫IOC容器

(3)什么是Bean?

容器中所存放的一个个对象就叫Bean或Bean对象

## 3，入门案例

介绍完Spring的核心概念后，接下来我们得思考一个问题就是，Spring到底是如何来实现IOC和DI的，那接下来就通过一些简单的入门案例，来演示下具体实现过程:

### 3.1 IOC入门案例

对于入门案例，我们得先`分析思路`然后再`代码实现`，

#### 3.1.1 入门案例思路分析

(1)Spring是使用容器来管理bean对象的，那么管什么? 

* 主要管理项目中所使用到的类对象，比如(Service和Dao)

(2)如何将被管理的对象告知IOC容器?

* 使用配置文件

(3)被管理的对象交给IOC容器，要想从容器中获取对象，就先得思考如何获取到IOC容器?

* Spring框架提供相应的接口

(4)IOC容器得到后，如何从容器中获取bean?

* 调用Spring框架提供对应接口中的方法

(5)使用Spring导入哪些坐标?

* 用别人的东西，就需要在pom.xml添加对应的依赖

#### 3.1.1 入门案例思路分析

(1)Spring是使用容器来管理bean对象的，那么管什么? 

* 主要管理项目中所使用到的类对象，比如(Service和Dao)

(2)如何将被管理的对象告知IOC容器?

* 使用配置文件

(3)被管理的对象交给IOC容器，要想从容器中获取对象，就先得思考如何获取到IOC容器?

* Spring框架提供相应的接口

(4)IOC容器得到后，如何从容器中获取bean?

* 调用Spring框架提供对应接口中的方法

(5)使用Spring导入哪些坐标?

* 用别人的东西，就需要在pom.xml添加对应的依赖

#### 3.1.2 入门案例代码实现

> 需求分析:将BookServiceImpl和BookDaoImpl交给Spring管理，并从容器中获取对应的bean对象进行方法调用。
>
> 1.创建Maven的java项目
>
> 2.pom.xml添加Spring的依赖jar包
>
> 3.创建BookService,BookServiceImpl，BookDao和BookDaoImpl四个类
>
> 4.resources下添加spring配置文件，并完成bean的配置
>
> 5.使用Spring提供的接口完成IOC容器的创建
>
> 6.从容器中获取对象进行方法调用

##### 步骤1:创建Maven项目

![1629734010072](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629734010072.png)

##### 步骤2:添加Spring的依赖jar包

pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

##### 步骤3:添加案例中需要的类

创建BookService,BookServiceImpl，BookDao和BookDaoImpl四个类

```java
public interface BookDao {
    public void save();
}
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }
}
public interface BookService {
    public void save();
}
public class BookServiceImpl implements BookService {
    private BookDao bookDao = new BookDaoImpl();
    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

##### 步骤4:添加spring配置文件

resources下添加spring配置文件applicationContext.xml，并完成bean的配置

![1629734336440](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629734336440.png)

##### 步骤5:在配置文件中完成bean的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
 
    <!--bean标签标示配置bean
    	id属性标示给bean起名字
    	class属性表示给bean定义类型
	-->
	<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"/>

</beans>
```

**==注意事项：bean定义时id属性在同一个上下文中(配置文件)不能重复==**

##### 步骤6:获取IOC容器

使用Spring提供的接口完成IOC容器的创建，创建App类，编写main方法

```java
public class App {
    public static void main(String[] args) {
        //获取IOC容器
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml"); 
    }
}
```

##### 步骤7:从容器中获取对象进行方法调用

```java
public class App {
    public static void main(String[] args) {
        //获取IOC容器
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml"); 
//        BookDao bookDao = (BookDao) ctx.getBean("bookDao");
//        bookDao.save();
        BookService bookService = (BookService) ctx.getBean("bookService");
        bookService.save();
    }
}
```

##### 步骤8:运行程序

测试结果为：

![image-20210729184337603](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729184337603.png)

Spring的IOC入门案例已经完成，但是在`BookServiceImpl`的类中依然存在`BookDaoImpl`对象的new操作，它们之间的耦合度还是比较高，这块该如何解决，就需要用到下面的`DI:依赖注入`。

### 3.2 DI入门案例

对于DI的入门案例，我们依然先`分析思路`然后再`代码实现`，

#### 3.2.1 入门案例思路分析

(1)要想实现依赖注入，必须要基于IOC管理Bean

- DI的入门案例要依赖于前面IOC的入门案例

(2)Service中使用new形式创建的Dao对象是否保留?

- 需要删除掉，最终要使用IOC容器中的bean对象

(3)Service中需要的Dao对象如何进入到Service中?

- 在Service中提供方法，让Spring的IOC容器可以通过该方法传入bean对象

(4)Service与Dao间的关系如何描述?

- 使用配置文件

#### 3.2.2 入门案例代码实现

> 需求:基于IOC入门案例，在BookServiceImpl类中删除new对象的方式，使用Spring的DI完成Dao层的注入
>
> 1.删除业务层中使用new的方式创建的dao对象
>
> 2.在业务层提供BookDao的setter方法
>
> 3.在配置文件中添加依赖注入的配置
>
> 4.运行程序调用方法

##### 步骤1: 去除代码中的new

在BookServiceImpl类中，删除业务层中使用new的方式创建的dao对象

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

##### 步骤2:为属性提供setter方法

在BookServiceImpl类中,为BookDao提供setter方法

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
    //提供对应的set方法
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```

##### 步骤3:修改配置完成注入

在配置文件中添加依赖注入的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--bean标签标示配置bean
    	id属性标示给bean起名字
    	class属性表示给bean定义类型
	-->
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <!--配置server与dao的关系-->
        <!--property标签表示配置当前bean的属性
        		name属性表示配置哪一个具体的属性
        		ref属性表示参照哪一个bean
		-->
        <property name="bookDao" ref="bookDao"/>
    </bean>

</beans>
```

==注意:配置中的两个bookDao的含义是不一样的==

* name="bookDao"中`bookDao`的作用是让Spring的IOC容器在获取到名称后，将首字母大写，前面加set找对应的`setBookDao()`方法进行对象注入
* ref="bookDao"中`bookDao`的作用是让Spring能在IOC容器中找到id为`bookDao`的Bean对象给`bookService`进行注入
* 综上所述，对应关系如下:

![1629736314989](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629736314989.png)

##### 步骤4:运行程序

运行，测试结果为：

![image-20210729184337603](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729184337603.png)



总结一下,关于IOC的核心观念

在这里,主要学习了两个概念,一个是IOC(反转控制),一个是Dl(依赖注入)

IOC被称为==控制反转==,由==外部==提供对象,此过程中对象创建控制权由程序转移到外部,被称为控制反转

具体使用如下

1. 创建spring的XML文件,在这个文件当中配置相应的bean对象

![image-20220919162615842](C:\Users\Yilin.Yang\Desktop\学习记录(笔记)\SSM框架学习\01Spring学习(一).assets\image-20220919162615842.png)

配置完成之后通过相应xml对象,调用相应的方法,通过外部调用,而不是·自己==new==这就是所谓IOC(控制反转)

(1)什么是控制反转呢？

* 使用对象时，由主动new产生对象转换为由==外部==提供对象，此过程中对象创建控制权由程序转移到外部，此思想称为控制反转。
  * 业务层要用数据层的类对象，以前是自己`new`的
  * 现在自己不new了，交给`别人[外部]`来创建对象
  * `别人[外部]`就反转控制了数据层对象的创建权
  * 这种思想就是控制反转
  * 别人[外部]指定是什么呢?继续往下学

(2)Spring和IOC之间的关系是什么呢?

* Spring技术对IOC思想进行了实现
* Spring提供了一个容器，称为==IOC容器==，用来充当IOC思想中的"外部"
* IOC思想中的`别人[外部]`指的就是Spring的IOC容器

(3)IOC容器的作用以及内部存放的是什么?

* IOC容器负责对象的创建、初始化等一系列工作，其中包含了数据层和业务层的类对象
* 被创建或被管理的对象在IOC容器中统称为==Bean==
* IOC容器中放的就是一个个的Bean对象

(4)当IOC容器中创建好service和dao对象后，程序能正确执行么?

* 不行，因为service运行需要依赖dao对象
* IOC容器中虽然有service和dao对象
* 但是service对象和dao对象没有任何关系
* 需要把dao对象交给service,也就是说要绑定service和dao对象之间的关系

像这种在容器中建立对象与对象之间的绑定关系就要用到**DI**:

2. ==DI（Dependency Injection）依赖注入==

![image-20220919163226533](C:\Users\Yilin.Yang\Desktop\学习记录(笔记)\SSM框架学习\01Spring学习(一).assets\image-20220919163226533.png)

(1)什么是依赖注入

在这里,数据层(DAO)依旧依赖于Serivice(业务实现层),而java如不必要,应该**一个类只创建一个对象**(即单例模式),在这情况下,如果不采用单例模式可能需要创建多个对象,通过依赖注入可以解决整个问题。

在这里Serive依赖于Dao,也就无需去创建多一个的对象。

在代码上的修改就是

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

##### 步骤2:为属性提供setter方法

在BookServiceImpl类中,为BookDao提供setter方法

```java
public class BookServiceImpl implements BookService {
    //删除业务层中使用new的方式创建的dao对象
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
    //提供对应的set方法
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```

##### 步骤3:修改配置完成注入

在配置文件中添加依赖注入的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--bean标签标示配置bean
    	id属性标示给bean起名字
    	class属性表示给bean定义类型
	-->
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <!--配置server与dao的关系-->
        <!--property标签表示配置当前bean的属性
        		name属性表示配置哪一个具体的属性
        		ref属性表示参照哪一个bean
		-->
        <property name="bookDao" ref="bookDao"/>
    </bean>

</beans>
```

==注意:配置中的两个bookDao的含义是不一样的==

* name="bookDao"中`bookDao`的作用是让Spring的IOC容器在获取到名称后，将首字母大写，前面加set找对应的`setBookDao()`方法进行对象注入
* **ref="bookDao"中`bookDao`的作用是让Spring能在IOC容器中找到id为`bookDao`的Bean对象给`bookService`进行注入**
* **综上所述，对应关系如下:**

**![1629736314989](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/1629736314989.png)**

##### **步骤4:运行程序**

**运行，测试结果为：**

**![image-20210729184337603](C:/Users/Yilin.Yang/Desktop/Java资料/spring基础框架8笔记/Spring笔记/spring_day01/assets/image-20210729184337603.png)**













-----------------------------------------------------分割线-----------------------------------------------------

IOC的相关内容

通过前面两个案例，我们已经学习了`bean如何定义配置`，`DI如何定义配置`以及`容器对象如何获取`的内容，接下来主要是把这三块内容展开进行详细的讲解，深入的学习下这三部分的内容，首先是bean基础配置。

### 4.1 bean基础配置

对于bean的配置中，主要会讲解`bean基础配置`,`bean的别名配置`,`bean的作用范围配置`==(重点)==,这三部分内容：

#### 4.1.1 bean基础配置(id与class)

对于bean的基础配置，在前面的案例中已经使用过:

```
<bean id="" class=""/>
```

其中，bean标签的功能、使用方式以及id和class属性的作用，我们通过一张图来描述下

![image-20220919210134844](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\image-20220919210134844.png)

这其中需要大家重点掌握的是:==bean标签的id和class属性的使用==。

**思考：**

* class属性能不能写接口如`BookDao`的类全名呢?

答案肯定是不行，因为接口是没办法创建对象的。

* 前面提过为bean设置id时，id必须唯一，但是如果由于命名习惯而产生了分歧后，该如何解决?

在解决这个问题之前，我们需要准备下开发环境，对于开发环境我们可以有两种解决方案:

* 使用前面IOC和DI的案例

* 重新搭建一个新的案例环境,目的是方便大家查阅代码

  * 

  * * 搭建的内容和前面的案例是一样的，内容如下：

      ![1629769227068](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\1629769227068.png)

  #### 4.1.2 bean的name属性

  环境准备好后，接下来就可以在这个环境的基础上来学习下bean的别名配置，

首先来看下别名的配置说明:

![image-20220919212011637](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\image-20220919212011637.png)

##### 步骤1：配置别名

打开spring的配置文件applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--name:为bean指定别名，别名可以有多个，使用逗号，分号，空格进行分隔-->
    <bean id="bookService" name="service service4 bookEbi" class="com.itheima.service.impl.BookServiceImpl">
        <property name="bookDao" ref="bookDao"/>
    </bean>

    <!--scope：为bean设置作用范围，可选值为单例singloton，非单例prototype-->
    <bean id="bookDao" name="dao" class="com.itheima.dao.impl.BookDaoImpl"/>
</beans>
```

**说明:Ebi全称Enterprise Business Interface，翻译为企业业务接口**

##### 步骤2:根据名称容器中获取bean对象

```java
public class AppForName {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        //此处根据bean标签的id属性和name属性的任意一个值来获取bean对象
        BookService bookService = (BookService) ctx.getBean("service4");
        bookService.save();
    }
}
```

##### 步骤3:运行程序

测试结果为：

![image-20210729184337603](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\image-20210729184337603.png)

==注意事项:==

==注意事项:==

* bean依赖注入的ref属性指定bean，必须在容器中存在

  ![1629771744003](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\1629771744003.png)

* 如果不存在,则会报错，如下:

  ![1629771880920](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\1629771880920.png)

  这个错误大家需要特别关注下:

  ![1629771972886](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\1629771972886.png)

  获取bean无论是通过id还是name获取，如果无法获取到，将抛出异常==NoSuchBeanDefinitionException==

#### 4.1.3 bean作用范围scope配置

关于bean的作用范围是bean属性配置的一个==重点==内容。

看到这个作用范围，我们就得思考bean的作用范围是来控制bean哪块内容的?

我们先来看下`bean作用范围的配置属性`:

![image-20210729183628138](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\image-20210729183628138.png)

##### 4.1.3.1 验证IOC容器中对象是否为单例

###### 验证思路

​	同一个bean获取两次，将对象打印到控制台，看打印出的地址值是否一致。

###### 具体实现

* 创建一个AppForScope的类，在其main方法中来验证

  ```java
  public class AppForScope {
      public static void main(String[] args) {
          ApplicationContext ctx = new 
              ClassPathXmlApplicationContext("applicationContext.xml");
  
          BookDao bookDao1 = (BookDao) ctx.getBean("bookDao");
          BookDao bookDao2 = (BookDao) ctx.getBean("bookDao");
          System.out.println(bookDao1);
          System.out.println(bookDao2);
      }
  }
  ```

* 打印，观察控制台的打印结果

  ![1629772538893](C:\Users\86138\Desktop\java\基础框架8笔记\Spring笔记\spring_day01\assets\1629772538893.png)

* 结论:默认情况下，Spring创建的bean对象都是单例的

获取到结论后，问题就来了，那如果我想创建出来非单例的bean对象，该如何实现呢?

##### 4.1.3.2 配置bean为非单例

在Spring配置文件中，配置scope属性来实现bean的非单例创建

* 在Spring的配置文件中，修改`<bean>`的scope属性

  ```xml
  <bean id="bookDao" name="dao" class="com.itheima.dao.impl.BookDaoImpl" scope=""/>
  ```

* 运行AppForScope，打印看结果

  ![1629772928714](C:\Users\86138\Desktop\java\基础框架8笔记\Spring笔记\spring_day01\assets\1629772928714.png)

* 结论，使用bean的`scope`属性可以控制bean的创建是否为单例：

  * `singleton`默认为单例
  * `prototype`为非单例

介绍完`scope`属性以后，我们来思考几个问题:

* 为什么bean默认为单例?
  * bean为单例的意思是在Spring的IOC容器中只会有该类的一个对象
  * bean对象只有一个就避免了对象的频繁创建与销毁，达到了bean对象的复用，性能高

bean在容器中是单例的，会不会产生线程安全问题?

* 如果对象是有状态对象，即该对象有成员变量可以用来存储数据的，
* 因为所有请求线程共用一个bean对象，所以会存在线程安全问题。
* 如果对象是无状态对象，即该对象没有成员变量没有进行数据存储的，
* 因方法中的局部变量在方法调用完成后会被销毁，所以不会存在线程安全问题。

* 哪些bean对象适合交给容器进行管理?
  * 表现层对象
  * 业务层对象
  * 数据层对象
  * 工具对象
* 哪些bean对象不适合交给容器进行管理?
  * 封装实例的域对象，因为会引发线程安全问题，所以不适合。

#### 4.14 bean基础配置小结

关于bean的基础配置中，需要大家掌握以下属性:

![1631529887695](C:\Users\86138\Desktop\后端学习\笔记\SSM框架学习\01Spring学习(一).assets\1631529887695.png)