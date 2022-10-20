# 瑞吉外卖项目优化-Day01

## 课程内容

- 环境搭建
- 缓存短信验证码
- 缓存菜品信息
- SpringCache
- 缓存套餐数据









## 前言

> 1). 当前系统存在的问题

之前我们已经实现了移动端菜品展示、点餐、购物车、下单等功能，但是由于移动端是面向所有的消费者的，请求压力相对比较大，而我们当前所有的数据查询都是从数据库MySQL中直接查询的，那么可能就存在如下问题： ==频繁访问数据库，数据库访问压力大，系统性能下降，用户体验较差。==

![image-20210819232120838](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210819232120838.png) 



> 2). 解决该问题的方法

要解决我们上述提到的问题，就可以使用我们前面学习的一个技术：Redis，通过Redis来做缓存，从而降低数据库的访问压力，提高系统的访问性能，从而提升用户体验。加入Redis做缓存之后，我们在进行数据查询时，就需要先查询缓存，如果缓存中有数据，直接返回，如果缓存中没有数据，则需要查询数据库，再将数据库查询的结果，缓存在redis中。











## 1. 环境搭建

### 1.1 版本控制

接下来，我们就需要对我们的功能进行优化，但是需要说明的是，我们不仅仅要对上述提到的缓存进行优化，还需要对我们程序的各个方面进行优化。我们本章节主要是针对于缓存进行优化，为了方便的对我们各个优化版本的代码进行管理，我们使用Git来控制代码版本。 那么此时我们就需要将我们之前开发完成的代码提交到Git，并且推送到码云Gitee的远程仓库，执行步骤如下： 



**1). 创建Gitee远程仓库**

<img src="../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820000329886.png" alt="image-20210820000329886" style="zoom:80%;" /> 



**2). idea-创建本地仓库**

![image-20210820000700459](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820000700459.png) 



**3). 准备忽略文件.gitignore**

在我们的项目中, 有一些文件是无需提交的到git，比如: .idea，target/，*.iml等。我们可以直接将今天课程资料中提供的.gitignore 文件导入到我们的项目中。

![image-20210820001119649](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820001119649.png) 



**4). idea-提交并推送本地代码**

A. 添加项目文件进暂存区

<img src="../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820001232154.png" alt="image-20210820001232154"  /> 



B. 提交代码

<img src="../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820001805504.png" alt="image-20210820001805504"  /> 

![image-20210820002006653](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820002006653.png) 



C. 推送代码到远程仓库 

![image-20210820002159587](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820002159587.png) 



**5). 查看gitee远程仓库**

![image-20210820002723619](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820002723619.png) 



**6). 创建分支**

目前默认git中只有一个主分支master，我们接下来进行缓存的优化，就不在master分支来操作了，我们需要在git上创建一个单独的分支v1.0，缓存的优化，我们就在该分支上进行操作。

![image-20210820003303544](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820003303544.png)  

当前创建的v1.0分支，是基于master分支创建出来的，所以目前master分支的代码， 和v1.0分支的代码是完全一样的，接下来把v1.0的代码也推送至远程仓库。



**7). 推送分支代码到远程**

![image-20210820003516900](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820003516900.png) 

![image-20210820003545764](../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210820003545764.png) 









### 1.2 环境准备

**1). 在项目的pom.xml文件中导入spring data redis的maven坐标**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



**2). 在项目的application.yml中加入redis相关配置**

```yml
  redis:
    host: 192.168.200.200
    port: 6379
    password: root@123456
    database: 0
```

==注意: 引入上述依赖时,需要注意yml文件前面的缩进,上述配置应该配置在spring层级下面。==

**3). 编写Redis的配置类RedisConfig,定义RedisTemplate**

```java
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        //默认的Key序列化器为：JdkSerializationRedisSerializer
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(connectionFactory);
        return redisTemplate;
    }
}
```

**解释说明:** 

1). 在SpringBoot工程启动时, 会加载一个自动配置类 RedisAutoConfiguration, 在里面已经声明了RedisTemplate这个bean

<img src="../../../新建文件夹/瑞吉外卖和springboot资料/新建文件夹 (2)/优化/03-讲义/瑞吉外卖项目优化-day01/assets/image-20210821091441695.png" alt="image-20210821091441695" style="zoom:80%;" /> 

上述框架默认声明的RedisTemplate用的key和value的序列化方式是默认的 JdkSerializationRedisSerializer，如果key采用这种方式序列化，最终我们在测试时通过redis的图形化界面查询不是很方便，如下形式：
