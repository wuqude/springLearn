# 瑞吉外卖-Day02

## 课程内容

- 完善登录功能

- 新增员工
- 员工信息分页查询
- 启用/禁用员工账号
- 编辑员工信息

# 瑞吉外卖-Day02

## 课程内容

- 完善登录功能

- 新增员工
- 员工信息分页查询
- 启用/禁用员工账号
- 编辑员工信息







## 1. 完善登录功能

### 1.1 问题分析

前面我们已经完成了后台系统的员工登录功能开发，但是目前还存在一个问题，接下来我们来说明一个这个问题， 以及如何处理。



**1). 目前现状**

用户如果不登录，直接访问系统首页面，照样可以正常访问。 

![image-20210727232226862](assets/image-20210727232226862.png) 



**2). 理想效果**

上述这种设计并不合理，我们希望看到的效果应该 是，只有登录成功后才可以访问系统中的页面，如果没有登录, 访问系统中的任何界面都直接跳转到登录页面。

<img src="assets/image-20210727232747276.png" alt="image-20210727232747276" style="zoom:80%;" /> 



**那么，具体应该怎么实现呢？**

可以使用我们之前讲解过的 过滤器、拦截器来实现，在过滤器、拦截器中拦截前端发起的请求，判断用户是否已经完成登录，如果没有登录则返回提示信息，跳转到登录页面。



### 1.2 思路分析

<img src="assets/image-20210727233554707.png" alt="image-20210727233554707" style="zoom:80%;" /> 

**过滤器具体的处理逻辑如下：**

A. 获取本次请求的URI

B. 判断本次请求, 是否需要登录, 才可以访问

C. 如果不需要，则直接放行

D. 判断登录状态，如果已登录，则直接放行

E. 如果未登录, 则返回未登录结果



如果未登录,我们需要给前端返回什么样的结果呢? 这个时候, 我们可以去看看前端是如何处理的 ?

<img src="assets/image-20210728001324901.png" alt="image-20210728001324901" style="zoom:80%;" /> 





### 1.3 代码实现

**1). 定义登录校验过滤器**

自定义一个过滤器 LoginCheckFilter 并实现 Filter 接口, 在doFilter方法中完成校验的逻辑。 那么接下来, 我们就根据上述分析的步骤, 来完成具体的功能代码实现: 



所属包: com.itheima.reggie.filter

```java
import com.alibaba.fastjson.JSON;
import com.itheima.reggie.common.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.util.AntPathMatcher;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 检查用户是否已经完成登录
 */
@WebFilter(filterName = "loginCheckFilter",urlPatterns = "/*")
@Slf4j
public class LoginCheckFilter implements Filter{
    //路径匹配器，支持通配符
    public static final AntPathMatcher PATH_MATCHER = new AntPathMatcher();

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;

        //1、获取本次请求的URI
        String requestURI = request.getRequestURI();// /backend/index.html

        log.info("拦截到请求：{}",requestURI);

        //定义不需要处理的请求路径
        String[] urls = new String[]{
                "/employee/login",
                "/employee/logout",
                "/backend/**",
                "/front/**"
        };
		
        //2、判断本次请求是否需要处理
        boolean check = check(urls, requestURI);

        //3、如果不需要处理，则直接放行
        if(check){
            log.info("本次请求{}不需要处理",requestURI);
            filterChain.doFilter(request,response);
            return;
        }
		
        //4、判断登录状态，如果已登录，则直接放行
        if(request.getSession().getAttribute("employee") != null){
            log.info("用户已登录，用户id为：{}",request.getSession().getAttribute("employee"));
            filterChain.doFilter(request,response);
            return;
        }

        log.info("用户未登录");
        //5、如果未登录则返回未登录结果，通过输出流方式向客户端页面响应数据
        response.getWriter().write(JSON.toJSONString(R.error("NOTLOGIN")));
        return;

    }

    /**
     * 路径匹配，检查本次请求是否需要放行
     * @param urls
     * @param requestURI
     * @return
     */
    public boolean check(String[] urls,String requestURI){
        for (String url : urls) {
            boolean match = PATH_MATCHER.match(url, requestURI);
            if(match){
                return true;
            }
        }
        return false;
    }
}
```





> <font color='red'><b>AntPathMatcher 拓展:</b></font>
>
> **介绍:** Spring中提供的路径匹配器 ;
>
> **通配符规则:** 		
>
> | 符号 | 含义                   |
> | ---- | ---------------------- |
> | ?    | 匹配一个字符           |
> | *    | 匹配0个或多个字符      |
> | **   | 匹配0个或多个目录/字符 |



**2). 开启组件扫描**

需要在引导类上, 加上Servlet组件扫描的注解, 来扫描过滤器配置的@WebFilter注解， 扫描上之后， 过滤器在运行时就生效了。

```java
@Slf4j
@SpringBootApplication
@ServletComponentScan
public class ReggieApplication {
    public static void main(String[] args) {
        SpringApplication.run(ReggieApplication.class,args);
        log.info("项目启动成功...");
    }
}
```



> <font color='red'><b>@ServletComponentScan 的作用: </b></font>
>
> ​	在SpringBoot项目中, 在引导类/配置类上加了该注解后, 会自动扫描项目中(当前包及其子包下)的@WebServlet , @WebFilter , @WebListener 注解, 自动注册Servlet的相关组件 ;







### 1.4 功能测试

代码编写完毕之后，我们需要将工程重启一下，然后在浏览器地址栏直接输入系统管理后台首页，然后看看是否可以跳转到登录页面即可。我们也可以通过debug的形式来跟踪一下代码执行的过程。

![image-20210728000838992](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728000838992.png) 



对于前端的代码, 也可以进行debug调试。

F12打开浏览器的调试工具, 找到我们前面提到的request.js, 在request.js的响应拦截器位置打上断点。

![image-20210728001929657](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728001929657.png) 





## 2. 新增员工

### 2.1 需求分析

后台系统中可以管理员工信息，通过新增员工来添加后台系统用户。点击[添加员工]按钮跳转到新增页面，如下：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728002442334.png" alt="image-20210728002442334" style="zoom:80%;" /> 

当填写完表单信息, 点击"保存"按钮后, 会提交该表单的数据到服务端, 在服务端中需要接受数据, 然后将数据保存至数据库中。



### 2.2 数据模型

新增员工，其实就是将我们新增页面录入的员工数据插入到employee表。employee表中的status字段已经设置了默认值1，表示状态正常。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728004144521.png" alt="image-20210728004144521" style="zoom:80%;" /> 



需要注意，employee表中对username字段加入了唯一约束，因为username是员工的登录账号，必须是唯一的。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728004250254.png" alt="image-20210728004250254" style="zoom:80%;" /> 





### 2.2 数据模型

新增员工，其实就是将我们新增页面录入的员工数据插入到employee表。employee表中的status字段已经设置了默认值1，表示状态正常。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728004144521.png" alt="image-20210728004144521" style="zoom:80%;" /> 



需要注意，employee表中对username字段加入了唯一约束，因为username是员工的登录账号，必须是唯一的。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728004250254.png" alt="image-20210728004250254" style="zoom:80%;" /> 





### 2.3 程序执行流程

在开发代码之前，我们需要结合着前端页面发起的请求， 梳理一下整个程序的执行过程：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728005638224.png" alt="image-20210728005638224" style="zoom:80%;" /> 



A. 点击"保存"按钮, 页面发送ajax请求，将新增员工页面中输入的数据以json的形式提交到服务端, 请求方式POST, 请求路径 /employee

B. 服务端Controller接收页面提交的数据并调用Service将数据进行保存

C. Service调用Mapper操作数据库，保存数据





 employee.setPassword(DigestUtils.md5DigestAsHex("123456".getBytes()));
	
    employee.setCreateTime(LocalDateTime.now());
    employee.setUpdateTime(LocalDateTime.now());
    
    //获得当前登录用户的id
    Long empId = (Long) request.getSession().getAttribute("employee");
    
    employee.setCreateUser(empId);
    employee.setUpdateUser(empId);
    
    employeeService.save(employee);
    return R.success("新增员工成功");
}



### 2.5 功能测试

代码编写完毕之后，我们需要将工程重启, 完毕之后直接访问管理系统首页, 点击 "员工管理" 页面中的 "添加员工" 按钮, 输入员工基本信息, 然后点击 "保存" 进行数据保存, 保存完毕后, 检查数据库中是否录入员工数据。



当我们在测试中，添加用户时， 输入了一个已存在的用户名时，前端界面出现错误提示信息： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728010841569.png" alt="image-20210728010841569" style="zoom:80%;" /> 

而此时，服务端已经报错了， 报错信息如下： 

![image-20210728010938086](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210728010938086.png) 

出现上述的错误， 主要就是因为在 employee 表结构中，我们针对于username字段，建立了唯一索引，添加重复的username数据时，违背该约束，就会报错。但是此时前端提示的信息并不具体，用户并不知道是因为什么原因造成的该异常，我们需要给用户提示详细的错误信息 。



(建立全局异常类)



### 2.6 全局异常处理

#### 2.6.1 思路分析

要想解决上述测试中存在的问题，我们需要对程序中可能出现的异常进行捕获，通常有两种处理方式：

**A. 在Controller方法中加入 try...catch 进行异常捕获**

形式如下： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729094125294.png" alt="image-20210729094125294" style="zoom:80%;" /> 

如果采用这种方式，虽然可以解决，但是存在弊端，需要我们在保存其他业务数据时，也需要在Controller方法中加上try...catch进行处理，代码冗余，不通用。



**B. 使用异常处理器进行全局异常捕获**

采用这种方式来实现，我们只需要在项目中定义一个通用的全局异常处理器，就可以解决本项目的所有异常。



#### 2.6.2 全局异常处理器

在项目中自定义一个全局异常处理器，在异常处理器上加上注解 @ControllerAdvice,可以通过属性annotations指定拦截哪一类的Controller方法。 并在异常处理器的方法上加上注解 @ExceptionHandler 来指定拦截的是那一类型的异常。

> 异常处理方法逻辑: 
>
> - 指定捕获的异常类型为 SQLIntegrityConstraintViolationException
> - 解析异常的提示信息, 获取出是那个值违背了唯一约束 
> - 组装错误信息并返回

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729100232642.png" alt="image-20210729100232642" style="zoom:80%;" /> 



> <font color='red'><b>注解说明:</b></font>
>
> ​	上述的全局异常处理器上使用了的两个注解 @ControllerAdvice , @ResponseBody , 他们的作用分别为: 
>
> ​	@ControllerAdvice : 指定拦截那些类型的控制器;
>
> ​	@ResponseBody: 将方法的返回值 R 对象转换为json格式的数据, 响应给页面;
>
> ​		上述使用的两个注解, 也可以合并成为一个注解 @RestControllerAdvice 
>
> > ​	<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729100052940.png" alt="image-20210729100052940" style="zoom:80%;" /> 
>
> 



#### 2.6.3 测试

全局异常处理器编写完毕之后，我们需要将项目重启, 完毕之后直接访问管理系统首页, 点击 "员工管理" 页面中的 "添加员工" 按钮。当我们在测试中，添加用户时， 输入了一个已存在的用户名时，前端界面出现如下错误提示信息：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729102220135.png" alt="image-20210729102220135" style="zoom:80%;" /> 



## 3. 员工分页查询

### 3.1 需求分析

系统中的员工很多的时候，如果在一个页面中全部展示出来会显得比较乱，不便于查看，所以一般的系统中都会以分页的方式来展示列表数据。而在我们的分页查询页面中, 除了分页条件以外，还有一个查询条件 "员工姓名"。

![image-20210729134904625](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729134904625.png) 

- 请求参数

  - 搜索条件： 员工姓名(模糊查询)

  - 分页条件： 每页展示条数 ， 页码

- 响应数据

  - 总记录数

  - 结果列表



### 3.2 程序执行流程

#### 3.2.1 页面流程分析

在开发代码之前，需要梳理一下整个程序的执行过程。

A. 点击菜单，打开员工管理页面时，执行查询： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729163400772.png" alt="image-20210729163400772" style="zoom:80%;" /> 



B. 搜索栏输入员工姓名,回车,执行查询:

![image-20210729164259997](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729164259997.png) 



1). 页面发送ajax请求，将分页查询参数(page、pageSize、name)提交到服务端

2). 服务端Controller接收页面提交的数据, 并组装条件调用Service查询数据

3). Service调用Mapper操作数据库，查询分页数据

4). Controller将查询到的分页数据, 响应给前端页面

5). 页面接收到分页数据, 并通过ElementUI的Table组件展示到页面上



#### 3.2.2 前端代码介绍

1). 访问员工列表页面/member/list.html时, 会触发Vuejs中的钩子方法, 在页面初始化时调用created方法

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729231639034.png" alt="image-20210729231639034" style="zoom:80%;" /> 

从上述的前端代码中我们可以看到, 执行完分页查询, 我们需要给前端返回的信息中需要包含两项 : records 中封装结果列表, total中封装总记录数 。



而在组装请求参数时 , page、pageSize 都是前端分页插件渲染时的参数；

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729232916380.png" alt="image-20210729232916380" style="zoom:80%;" /> 





2). 在getMemberList方法中, 通过axios发起异步请求

![image-20210729231745143](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729231745143.png) 

axios发起的异步请求会被声明在 request.js 中的request拦截器拦截, 在其中对get请求进行进一步的封装处理

![image-20210729232036767](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729232036767.png) 



**最终发送给服务端的请求为 :** GET请求 , 请求链接 /employee/page?page=1&pageSize=10&name=xxx



### 3.3 代码实现

#### 3.3.1 分页插件配置

当前我们要实现的分页查询功能，而在MybatisPlus要实现分页功能，就需要用到MybatisPlus中提供的分页插件，要使用分页插件，就要在配置类中声明分页插件的bean对象。

所属包: com.itheima.reggie.config

```java
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置MP的分页插件
 */
@Configuration
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```



#### 3.3.2 分页查询实现

在上面我们已经分析了，页面在进行分页查询时， 具体的请求信息如下:

| 请求     | 说明                   |
| -------- | ---------------------- |
| 请求方式 | GET                    |
| 请求路径 | /employee/page         |
| 请求参数 | page , pageSize , name |



那么查询完毕后我们需要给前端返回什么样的结果呢? 

在上述我们也分析了, 查询返回的结果数据data中应该封装两项信息, 分别为: records 封装分页列表数据, total 中封装符合条件的总记录数。 那么这个时候, 在定义controller方法的返回值类型R时, 我们可以直接将 MybatisPlus 分页查询的结果 Page 直接封装返回, 因为Page中的属性如下: 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day02/assets/image-20210729235403154.png" alt="image-20210729235403154" style="zoom:80%;" /> 



那么接下来就依据于这些已知的需求和条件完成分页查询的代码实现。 具体的逻辑如下: 

A. 构造分页条件

B. 构建搜索条件 - name进行模糊匹配

C. 构建排序条件 - 更新时间倒序排序

D. 执行查询

E. 组装具体的代码实现如下: 

```java
/**
 * 员工信息分页查询
 * @param page 当前查询页码
 * @param pageSize 每页展示记录数
 * @param name 员工姓名 - 可选参数
 * @return
 */
@GetMapping("/page")
public R<Page> page(int page,int pageSize,String name){
    log.info("page = {},pageSize = {},name = {}" ,page,pageSize,name);
    //构造分页构造器
    Page pageInfo = new Page(page,pageSize);

    //构造条件构造器
    LambdaQueryWrapper<Employee> queryWrapper = new LambdaQueryWrapper();
    //添加过滤条件
    queryWrapper.like(StringUtils.isNotEmpty(name),Employee::getName,name);
    //添加排序条件
    queryWrapper.orderByDesc(Employee::getUpdateTime);

    //执行查询
    employeeService.page(pageInfo,queryWrapper);
    return R.success(pageInfo);
}
```

结果并返回
