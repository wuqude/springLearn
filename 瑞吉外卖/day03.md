# 瑞吉外卖-Day03

## 课程内容

- 公共字段自动填充
- 新增分类
- 分类信息分页查询
- 删除分类
- 修改分类

## 1. 公共字段自动填充

### 1.1 问题分析

前面我们已经完成了后台系统的员工管理功能的开发，在新增员工时需要设置创建时间、创建人、修改时间、修改人等字段，在编辑员工时需要设置修改时间、修改人等字段。这些字段属于公共字段，也就是也就是在我们的系统中很多表中都会有这些字段，如下：

![image-20210801085103062](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801085103062.png) 

而针对于这些字段，我们的赋值方式为： 

A. 在新增数据时, 将createTime、updateTime 设置为当前时间, createUser、updateUser设置为当前登录用户ID。

B. 在更新数据时, 将updateTime 设置为当前时间, updateUser设置为当前登录用户ID。



目前,在我们的项目中处理这些字段都是在每一个业务方法中进行赋值操作,如下:

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801085615162.png" alt="image-20210801085615162" style="zoom:80%;" /> 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801085715419.png" alt="image-20210801085715419" style="zoom:80%;" /> 



如果都按照上述的操作方式来处理这些公共字段, 需要在每一个业务方法中进行操作, 编码相对冗余、繁琐，那能不能对于这些公共字段在某个地方统一处理，来简化开发呢？

**答案是可以的，我们使用Mybatis Plus提供的公共字段自动填充功能。**







### 1.2 基本功能实现

#### 1.2.1 思路分析

Mybatis Plus公共字段自动填充，也就是在插入或者更新的时候为指定字段赋予指定的值，使用它的好处就是可以统一对这些字段进行处理，避免了重复代码。在上述的问题分析中，我们提到有四个公共字段，需要在新增/更新中进行赋值操作, 具体情况如下: 

| 字段名     | 赋值时机                    | 说明           |
| ---------- | --------------------------- | -------------- |
| createTime | 插入(INSERT)                | 当前时间       |
| updateTime | 插入(INSERT) , 更新(UPDATE) | 当前时间       |
| createUser | 插入(INSERT)                | 当前登录用户ID |
| updateUser | 插入(INSERT) , 更新(UPDATE) | 当前登录用户ID |



实现步骤：

1、在实体类的属性上加入@TableField注解，指定自动填充的策略。

2、按照框架要求编写元数据对象处理器，在此类中统一为公共字段赋值，此类需要实现MetaObjectHandler接口。







#### 1.2.2 代码实现

**1). 实体类的属性上加入@TableField注解，指定自动填充的策略。**

在员工Employee实体类的公共字段属性上, 加上注解, 指定填充策略。(<font color='red'>ps.在资料中提供的实体类,已经添加了该注解,并指定了填充策略</font>)

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801092157093.png" alt="image-20210801092157093" style="zoom:80%;" /> 

> FieldFill.INSERT: 插入时填充该属性值
>
> FieldFill.INSERT_UPDATE: 插入/更新时填充该属性值

**2). 按照框架要求编写元数据对象处理器，在此类中统一为公共字段赋值，此类需要实现MetaObjectHandler接口。**

所属包: com.itheima.reggie.common

```java
import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;
import java.time.LocalDateTime;

/**
 * 自定义元数据对象处理器
 */
@Component
@Slf4j
public class MyMetaObjecthandler implements MetaObjectHandler {
    /**
     * 插入操作，自动填充
     * @param metaObject
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("公共字段自动填充[insert]...");
        log.info(metaObject.toString());
        
        metaObject.setValue("createTime", LocalDateTime.now());
        metaObject.setValue("updateTime",LocalDateTime.now());
        metaObject.setValue("createUser",new Long(1));
        metaObject.setValue("updateUser",new Long(1));
    }

    /**
     * 更新操作，自动填充
     * @param metaObject
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("公共字段自动填充[update]...");
        log.info(metaObject.toString());

        metaObject.setValue("updateTime",LocalDateTime.now());
        metaObject.setValue("updateUser",new Long(1));
    }
}
```



#### 1.2.3 功能测试

编写完了元数据对象处理器之后，我们就可以将之前在新增和修改方法中手动赋值的代码删除或注释掉。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801093623217.png" alt="image-20210801093623217" style="zoom: 80%;" />  

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801093747896.png" alt="image-20210801093747896" style="zoom:80%;" /> 



然后，我们启动项目，在员工管理模块中，测试增加/更新员工信息功能，然后通过debug 或者 直接查询数据库数据变更的形式，看看我们在新增/修改数据时，这些公共字段数据是否能够完成自动填充。





### 1.3 功能完善

#### 1.3.1 思路分析

前面我们已经完成了公共字段自动填充功能的代码开发，但是还有一个问题没有解决，就是我们在自动填充createUser和updateUser时设置的用户id是固定值，现在我们需要完善，改造成动态获取当前登录用户的id。

大家可能想到，用户登录成功后我们将用户id存入了HttpSession中，现在我从HttpSession中获取不就行了？

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801131449863.png" alt="image-20210801131449863" style="zoom:80%;" /> 

注意，我们在MyMetaObjectHandler类中是不能直接获得HttpSession对象的，所以我们需要通过其他方式来获取登录用户id。



那么我先搞清楚一点,当我们在修改员工信息时, 我们业务的执行流程是什么样子的,如下图:

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801133531663.png" alt="image-20210801133531663" style="zoom:80%;" />  

客户端发送的每次http请求，对应的在服务端都会分配一个新的线程来处理，在处理过程中涉及到下面类中的方法都属于相同的一个线程：

1). LoginCheckFilter的doFilter方法

2). EmployeeController的update方法

3). MyMetaObjectHandler的updateFill方法

我们可以在上述类的方法中加入如下代码(获取当前线程ID,并输出):

```java
long id = Thread.currentThread().getId();
log.info("线程id为：{}",id);
```

执行编辑员工功能进行验证，通过观察控制台输出可以发现，一次请求对应的线程id是相同的：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801133827264.png" alt="image-20210801133827264" style="zoom:80%;" />  



经过上述的分析之后,发现我们可以使用JDK提供的一个类, 来解决此问题,它是JDK中提供的 ThreadLocal。



#### 1.3.2 ThreadLocal

ThreadLocal并不是一个Thread，而是Thread的局部变量。当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。

ThreadLocal为每个线程提供单独一份存储空间，具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问当前线程对应的值。



**ThreadLocal常用方法：**

A. public void set(T value) : 设置当前线程的线程局部变量的值

B. public T get() : 返回当前线程所对应的线程局部变量的值

C. public void remove() : 删除当前线程所对应的线程局部变量的值 



我们可以在LoginCheckFilter的doFilter方法中获取当前登录用户id，并调用ThreadLocal的set方法来设置当前线程的线程局部变量的值（用户id），然后在MyMetaObjectHandler的updateFill方法中调用ThreadLocal的get方法来获得当前线程所对应的线程局部变量的值（用户id）。 如果在后续的操作中, 我们需要在Controller / Service中要使用当前登录用户的ID, 可以直接从ThreadLocal直接获取。



#### 1.3.3 操作步骤

实现步骤：

1). 编写BaseContext工具类，基于ThreadLocal封装的工具类

2). 在LoginCheckFilter的doFilter方法中调用BaseContext来设置当前登录用户的id

3). 在MyMetaObjectHandler的方法中调用BaseContext获取登录用户的id



#### 1.3.4 代码实现

**1). BaseContext工具类**

所属包: com.itheima.reggie.common

```java
/**
 * 基于ThreadLocal封装工具类，用户保存和获取当前登录用户id
 */
public class BaseContext {
    private static ThreadLocal<Long> threadLocal = new ThreadLocal<>();
    /**
     * 设置值
     * @param id
     */
    public static void setCurrentId(Long id){
        threadLocal.set(id);
    }
    /**
     * 获取值
     * @return
     */
    public static Long getCurrentId(){
        return threadLocal.get();
    }
}
```



**2).LoginCheckFilter中存放当前登录用户到ThreadLocal**

在doFilter方法中, 判定用户是否登录, 如果用户登录, 在放行之前, 获取HttpSession中的登录用户信息, 调用BaseContext的setCurrentId方法将当前登录用户ID存入ThreadLocal。

```java
Long empId = (Long) request.getSession().getAttribute("employee");
BaseContext.setCurrentId(empId);
```

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801162053581.png" alt="image-20210801162053581" style="zoom:80%;" /> 





**3). MyMetaObjectHandler中从ThreadLocal中获取**

将之前在代码中固定的当前登录用户1， 修改为动态调用BaseContext中的getCurrentId方法获取当前登录用户ID

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801162345846.png" alt="image-20210801162345846" style="zoom:80%;" /> 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801162436740.png" alt="image-20210801162436740" style="zoom:80%;" /> 



#### 1.3.5 功能测试

完善了元数据对象处理器之后，我们就可以重新启动项目，完成登录操作后, 在员工管理模块中，测试增加/更新员工信息功能, 直接查询数据库数据变更，看看我们在新增/修改数据时，这些公共字段数据是否能够完成自动填充, 并且看看填充的create_user 及 update_user字段值是不是本地登录用户的ID。

## 2. 新增分类

### 2.1 需求分析

后台系统中可以管理分类信息，分类包括两种类型，分别是 **菜品分类** 和 **套餐分类** 。当我们在后台系统中添加菜品时需要选择一个菜品分类，当我们在后台系统中添加一个套餐时需要选择一个套餐分类，在移动端也会按照菜品分类和套餐分类来展示对应的菜品和套餐。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801163745391.png" alt="image-20210801163745391" style="zoom:80%;" /> 

在分类管理中,我们新增分类时, 可以选择新增菜品分类(川菜、湘菜、粤菜...), 也可以选择新增套餐分类(营养早餐、超值午餐...)。 在添加套餐的时候, 输入的排序字段, 控制的是移动端套餐列表的展示顺序。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801165118745.png" alt="image-20210801165118745" style="zoom:80%;" /> 



### 2.2 数据模型

新增分类，其实就是将我们新增窗口录入的分类数据,插入到category表，具体表结构如下：

![image-20210801165801665](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801165801665.png) 

我们添加的套餐名称，是唯一的，不能够重复的，所以在设计表结构时，已经针对于name字段建立了唯一索引，如下：

![image-20210801165921450](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801165921450.png) 



### 2.3 前端页面分析

在开发代码之前，需要梳理一下整个程序的执行过程：

1). 在页面(backend/page/category/list.html)的新增分类表单中填写数据，点击 "确定" 发送ajax请求，将新增分类窗口输入的数据以json形式提交到服务端

2). 服务端Controller接收页面提交的数据并调用Service将数据进行保存

3). Service调用Mapper操作数据库，保存数据



可以看到新增菜品分类和新增套餐分类请求的服务端地址和提交的json数据结构相同，所以服务端只需要提供一个方法统一处理即可：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801171125255.png" alt="image-20210801171125255" style="zoom:80%;" /> 

具体请求信息整理如下: 

| 请求     | 说明                                           |
| -------- | ---------------------------------------------- |
| 请求方式 | POST                                           |
| 请求路径 | /category                                      |
| 请求参数 | json格式 - {"name":"川菜","type":"1","sort":2} |



### 2.4 代码实现

代码实现的具体步骤如下: 

- 实体类Category（直接从课程资料中导入即可）

- Mapper接口CategoryMapper

- 业务层接口CategoryService

- 业务层实现类CategoryServiceImpl

- 控制层CategoryController



**1). 实体类Category**

所属包: com.itheima.reggie.entity

```java
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.TableField;
import lombok.Data;
import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * 分类
 */
@Data
public class Category implements Serializable {

    private static final long serialVersionUID = 1L;

    private Long id;

    //类型 1 菜品分类 2 套餐分类
    private Integer type;

    //分类名称
    private String name;

    //顺序
    private Integer sort;

    //创建时间
    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime createTime;

    //更新时间
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime updateTime;
    
    //创建人
    @TableField(fill = FieldFill.INSERT)
    private Long createUser;
    
    //修改人
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Long updateUser;
}
```



**2). Mapper接口CategoryMapper**

所属包: com.itheima.reggie.mapper

```java
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.reggie.entity.Category;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface CategoryMapper extends BaseMapper<Category> {
}
```



**3). 业务层接口CategoryService**

所属包: com.itheima.reggie.service

```java
import com.baomidou.mybatisplus.extension.service.IService;
import com.itheima.reggie.entity.Category;

public interface CategoryService extends IService<Category> {
}
```



**4). 业务层实现类CategoryServiceImpl**

所属包: com.itheima.reggie.service.impl

```java
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.itheima.reggie.entity.Category;
import com.itheima.reggie.mapper.CategoryMapper;
import com.itheima.reggie.service.CategoryService;
import org.springframework.stereotype.Service;

@Service
public class CategoryServiceImpl extends ServiceImpl<CategoryMapper,Category> implements CategoryService{
}
```



**5). 控制层CategoryController**

```java
import com.itheima.reggie.common.R;
import com.itheima.reggie.entity.Category;
import com.itheima.reggie.service.CategoryService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

/**
 * 分类管理
 */
@RestController
@RequestMapping("/category")
@Slf4j
public class CategoryController {
    @Autowired
    private CategoryService categoryService;
	
    /**
     * 新增分类
     * @param category
     * @return
     */
    @PostMapping
    public R<String> save(@RequestBody Category category){
        log.info("category:{}",category);
        categoryService.save(category);
        return R.success("新增分类成功");
    }
}    
```



### 2.5 功能测试

新增分类的代码编写完毕之后, 我们需要重新启动项目，进入管理系统访问分类管理, 然后进行新增分类测试，需要将所有情况都覆盖全，例如：

1). 输入的分类名称不存在

2). 输入已存在的分类名称

3). 新增菜品分类

4). 新增套餐分类





## 3. 分类信息分页查询

### 3.1 需求分析

系统中的分类很多的时候，如果在一个页面中全部展示出来会显得比较乱，不便于查看，所以一般的系统中都会以分页的方式来展示列表数据。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801172259439.png" alt="image-20210801172259439" style="zoom:80%;" /> 



### 3.2 前端页面分析

在开发代码之前，需要梳理一下整个程序的执行过程：

1). 页面发送ajax请求，将分页查询参数(page、pageSize)提交到服务端

2). 服务端Controller接收页面提交的数据并调用Service查询数据

3). Service调用Mapper操作数据库，查询分页数据

4). Controller将查询到的分页数据响应给页面

5). 页面接收到分页数据并通过ElementUI的Table组件展示到页面上



页面加载时,就会触发Vue声明周期的钩子方法,然后执行分页查询,发送异步请求到服务端,前端代码如下: 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801172847501.png" alt="image-20210801172847501" style="zoom:80%;" />  

页面中使用的是ElementUI提供的分页组件进行分页条的展示:

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801173229949.png" alt="image-20210801173229949" style="zoom:80%;" />  



我们通过浏览器,也可以抓取到分页查询的请求信息, 如下: 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801172951915.png" alt="image-20210801172951915" style="zoom:80%;" /> 



具体的请求信息整理如下: 

| 请求     | 说明                |
| -------- | ------------------- |
| 请求方式 | GET                 |
| 请求路径 | /category/page      |
| 请求参数 | ?page=1&pageSize=10 |

### 3.3 代码实现

在CategoryController中增加分页查询的方法，在方法中传递分页条件进行查询，并且需要对查询到的结果，安排设置的套餐顺序字段sort进行排序。



### 3.4 功能测试

分页查询的代码编写完毕之后, 我们需要重新启动项目，然后登陆系统后台，点击分类管理，查询分类列表是否可以正常展示。测试过程中可以使用浏览器的监控工具查看页面和服务端的数据交互细节。

测试完毕后，大家会发现，我们查询数据库返回的类型为 1 或者 2， 但是实际展示到页面上的却是 "菜品分类" 或 "套餐分类"，这一块是在前端页面中进行处理的，处理代码如下： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801173758580.png" alt="image-20210801173758580" style="zoom:80%;" /> 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801173850606.png" alt="image-20210801173850606" style="zoom:94%;" /> 

## 4. 删除分类

### 4.1 需求分析

在分类管理列表页面，可以对某个分类进行删除操作。需要注意的是当分类关联了菜品或者套餐时，此分类不允许删除。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day03/assets/image-20210801220637396.png" alt="image-20210801220637396" style="zoom:80%;" /> 

