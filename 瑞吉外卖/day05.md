# 瑞吉外卖-第五天

## 课程内容

- 新增套餐
- 套餐分页查询
- 删除套餐

- 短信发送
- 手机验证码登录





## 1. 新增套餐

### 1.1 需求分析

套餐就是菜品的集合。

后台系统中可以管理套餐信息，通过新增套餐功能来添加一个新的套餐，在添加套餐时需要选择当前套餐所属的套餐分类和包含的菜品，并且需要上传套餐对应的图片，在移动端会按照套餐分类来展示对应的套餐。

<img src="../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210805232711418.png" alt="image-20210805232711418" style="zoom:80%;" /> 



### 1.2 数据模型

新增套餐，其实就是将新增页面录入的套餐信息插入到setmeal表，还需要向setmeal_dish表插入套餐和菜品关联数据。所以在新增套餐时，涉及到两个表：

| 表           | 说明           | 备注                                               |
| ------------ | -------------- | -------------------------------------------------- |
| setmeal      | 套餐表         | 存储套餐的基本信息                                 |
| setmeal_dish | 套餐菜品关系表 | 存储套餐关联的菜品的信息(一个套餐可以关联多个菜品) |



两张表具体的表结构如下: 

**1). 套餐表setmeal**

![image-20210805233615067](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210805233615067.png) 

在该表中，套餐名称name字段是不允许重复的，在建表时，已经创建了唯一索引。

![image-20210805234059563](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210805234059563.png) 



**2). 套餐菜品关系表setmeal_dish**

![image-20210805233807009](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210805233807009.png) 

在该表中，菜品的名称name,菜品的原价price 实际上都是冗余字段,因为我们在这张表中存储了菜品的ID(dish_id),根据该ID我们就可以查询出name,price的数据信息,而这里我们又存储了name,price,这样的话,我们在后续的查询展示操作中,就不需要再去查询数据库获取菜品名称和原价了,这样可以简化我们的操作。



### 1.3 准备工作

在开发业务功能前，先将需要用到的类和接口基本结构创建好，在做这一块儿的准备工作时，我们无需准备Setmeal的相关实体类、Mapper接口、Service接口及实现，因为之前在做分类管理的时候，我们已经引入了Setmeal的相关基础代码。 接下来，我们就来完成以下的几步准备工作： 



**1). 实体类 SetmealDish**

ps.直接从课程资料中导入即可，Setmeal实体前面课程中已经导入过了。

所属包： com.itheima.reggie.entity

```java
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.TableField;
import lombok.Data;
import java.io.Serializable;
import java.math.BigDecimal;
import java.time.LocalDateTime;

/**
 * 套餐菜品关系
 */
@Data
public class SetmealDish implements Serializable {
    private static final long serialVersionUID = 1L;

    private Long id;

    //套餐id
    private Long setmealId;

    //菜品id
    private Long dishId;

    //菜品名称 （冗余字段）
    private String name;
    
    //菜品原价
    private BigDecimal price;
    
    //份数
    private Integer copies;

    //排序
    private Integer sort;

    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime updateTime;

    @TableField(fill = FieldFill.INSERT)
    private Long createUser;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Long updateUser;

    //是否删除
    private Integer isDeleted;
}
```



**2). DTO SetmealDto**

该数据传输对象DTO,主要用于封装页面在新增套餐时传递过来的json格式的数据,其中包含套餐的基本信息,还包含套餐关联的菜品集合。直接从课程资料中导入即可。

所属包： com.itheima.reggie.dto

```java
import com.itheima.reggie.entity.Setmeal;
import com.itheima.reggie.entity.SetmealDish;
import lombok.Data;
import java.util.List;

@Data
public class SetmealDto extends Setmeal {

    private List<SetmealDish> setmealDishes;//套餐关联的菜品集合
	
    private String categoryName;//分类名称
}
```



**3). Mapper接口 SetmealDishMapper**

所属包: com.itheima.reggie.mapper

```java
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.reggie.entity.SetmealDish;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface SetmealDishMapper extends BaseMapper<SetmealDish> {
}
```



**4). 业务层接口 SetmealDishService**

所属包： com.itheima.reggie.service

```java
import com.baomidou.mybatisplus.extension.service.IService;
import com.itheima.reggie.entity.SetmealDish;

public interface SetmealDishService extends IService<SetmealDish> {
}
```



**5). 业务层实现类 SetmealDishServiceImpl**

所属包： com.itheima.reggie.service.impl

```java
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.itheima.reggie.entity.SetmealDish;
import com.itheima.reggie.mapper.SetmealDishMapper;
import com.itheima.reggie.service.SetmealDishService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

@Service
@Slf4j
public class SetmealDishServiceImpl extends ServiceImpl<SetmealDishMapper,SetmealDish> implements SetmealDishService {
}
```



**6). 控制层 SetmealController**

套餐管理的相关业务，我们都统一在 SetmealController 中进行统一处理操作。

所属包: com.itheima.reggie.service.impl

```java
import com.itheima.reggie.service.SetmealDishService;
import com.itheima.reggie.service.SetmealService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

/**
 * 套餐管理
 */
@RestController
@RequestMapping("/setmeal")
@Slf4j
public class SetmealController {
    @Autowired
    private SetmealService setmealService;
    @Autowired
    private SetmealDishService setmealDishService;
}    
```





### 1.4 前端页面分析

服务端的基础准备工作我们准备完毕之后，在进行代码开发之前，需要梳理一下新增套餐时前端页面和服务端的交互过程：

1). 点击新建套餐按钮，访问页面(backend/page/combo/add.html)，页面加载发送ajax请求，请求服务端获取**套餐分类**数据并展示到下拉框中(==已实现==)

![image-20210806002144537](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806002144537.png) 

获取套餐分类列表的功能我们不用开发，之前已经开发完成了，之前查询时type传递的是1，查询菜品分类; 本次查询时，传递的type为2，查询套餐分类列表。



2). 访问页面(backend/page/combo/add.html)，页面加载时发送ajax请求，请求服务端获取**菜品分类**数据并展示到添加菜品窗口中(==已实现==)

![image-20210806002730820](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806002730820.png) 

本次查询分类列表，传递的type为1，表示需要查询的是菜品的分类。查询菜品分类的目的，是添加套餐关联的菜品时，我们需要根据菜品分类，来过滤查询菜品信息。查询菜品分类列表的代码已经实现， 具体展示效果如下： 

![image-20210806003318531](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806003318531.png)  



3). 当点击添加菜品窗口左侧菜单的某一个分类, 页面发送ajax请求，请求服务端，根据菜品分类查询对应的**菜品**数据并展示到添加菜品窗口中

![image-20210806004116496](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806004116496.png) 



4). 页面发送请求进行**图片上传**，请求服务端将图片保存到服务器(==已实现==)

5). 页面发送请求进行**图片下载**，将上传的图片进行回显(==已实现==)

![image-20210806004434277](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806004434277.png) 



6). 点击保存按钮，发送ajax请求，将**套餐**相关数据以json形式提交到服务端

![image-20210806005028874](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806005028874.png) 



经过上述的页面解析及流程分析，我们发送这里需要发送的请求有5个，分别是 ：

A. 根据传递的参数,查询套餐分类列表

B. 根据传递的参数,查询菜品分类列表

C. 图片上传

D. 图片下载展示

E. 根据菜品分类ID,查询菜品列表

F. 保存套餐信息



而对于以上的前4个功能我们都已经实现, 所以我们接下来需要开发的功能主要是最后两项, 具体的请求信息如下:

**1). 根据分类ID查询菜品列表**

| 请求     | 说明                            |
| -------- | ------------------------------- |
| 请求方式 | GET                             |
| 请求路径 | /dish/list                      |
| 请求参数 | ?categoryId=1397844263642378242 |



**2). 保存套餐信息**

| 请求     | 说明         |
| -------- | ------------ |
| 请求方式 | POST         |
| 请求路径 | /setmeal     |
| 请求参数 | json格式数据 |

传递的json格式数据如下: 

```json
{
    "name":"营养超值工作餐",
    "categoryId":"1399923597874081794",
    "price":3800,
    "code":"",
    "image":"9cd7a80a-da54-4f46-bf33-af3576514cec.jpg",
    "description":"营养超值工作餐",
    "dishList":[],
    "status":1,
    "idType":"1399923597874081794",
    "setmealDishes":[
    	{"copies":2,"dishId":"1423329009705463809","name":"米饭","price":200},
    	{"copies":1,"dishId":"1423328152549109762","name":"可乐","price":500},
    	{"copies":1,"dishId":"1397853890262118402","name":"鱼香肉丝","price":3800}
    ]
}
```



### 1.5 代码开发

上面我们已经分析了接下来我们需要实现的两个功能，接下来我们就需要根据上述的分析，来完成具体的功能实现。



#### 1.5.1 根据分类查询菜品

##### 1.5.1.1 功能实现

在当前的需求中，我们只需要根据页面传递的菜品分类的ID(categoryId)来查询菜品列表即可，我们可以直接定义一个DishController的方法，声明一个Long类型的categoryId，这样做是没问题的。但是考虑到该方法的拓展性，我们在这里定义方法时，通过Dish这个实体来接收参数。





在DishController中定义方法list，接收Dish类型的参数：

在查询时，需要根据菜品分类categoryId进行查询，并且还要限定菜品的状态为起售状态(status为1)，然后对查询的结果进行排序。

```java
/**
* 根据条件查询对应的菜品数据
* @param dish
* @return
*/
@GetMapping("/list")
public R<List<Dish>> list(Dish dish){
    //构造查询条件
    LambdaQueryWrapper<Dish> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.eq(dish.getCategoryId() != null ,Dish::getCategoryId,dish.getCategoryId());
    //添加条件，查询状态为1（起售状态）的菜品
    queryWrapper.eq(Dish::getStatus,1);
    //添加排序条件
    queryWrapper.orderByAsc(Dish::getSort).orderByDesc(Dish::getUpdateTime);
	
    List<Dish> list = dishService.list(queryWrapper);
	
    return R.success(list);
}
```

##### 1.5.1.2 功能测试

代码编写完毕，我们重新启动服务器，进行测试，可以通过debug断点跟踪的形式查看页面传递的参数封装情况，及响应给页面的数据信息。

![image-20210806012153982](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806012153982.png) 







#### 1.5.2 保存套餐

##### 1.5.2.1 功能实现

在进行套餐信息保存时，前端提交的数据，不仅包含套餐的基本信息，还包含套餐关联的菜品列表数据 setmealDishes。所以这个时候我们使用Setmeal就不能完成参数的封装了，我们需要在Setmeal的基本属性的基础上，再扩充一个属性 setmealDishes 来接收页面传递的套餐关联的菜品列表，而我们在准备工作中，导入进来的SetmealDto能够满足这个需求。



1). SetmealController中定义方法save，新增套餐

在该Controller的方法中,我们不仅需要保存套餐的基本信息，还需要保存套餐关联的菜品数据，所以我们需要再该方法中调用业务层方法,完成两块数据的保存。

页面传递的数据是json格式，需要在方法形参前面加上@RequestBody注解, 完成参数封装。

```java
@PostMapping
public R<String> save(@RequestBody SetmealDto setmealDto){
    log.info("套餐信息：{}",setmealDto);

    setmealService.saveWithDish(setmealDto);

    return R.success("新增套餐成功");
}
```



2). SetmealService中定义方法saveWithDish

```java
/**
 * 新增套餐，同时需要保存套餐和菜品的关联关系
 * @param setmealDto
 */
public void saveWithDish(SetmealDto setmealDto);
```



3). SetmealServiceImpl实现方法saveWithDish

具体逻辑: 

A. 保存套餐基本信息

B. 获取套餐关联的菜品集合，并为集合中的每一个元素赋值套餐ID(setmealId)

C. 批量保存套餐关联的菜品集合

代码实现: 

```java
/**
 * 新增套餐，同时需要保存套餐和菜品的关联关系
 * @param setmealDto
 */
@Transactional
public void saveWithDish(SetmealDto setmealDto) {
    //保存套餐的基本信息，操作setmeal，执行insert操作
    this.save(setmealDto);

    List<SetmealDish> setmealDishes = setmealDto.getSetmealDishes();
    setmealDishes.stream().map((item) -> {
        item.setSetmealId(setmealDto.getId());
        return item;
    }).collect(Collectors.toList());

    //保存套餐和菜品的关联信息，操作setmeal_dish,执行insert操作
    setmealDishService.saveBatch(setmealDishes);
}
```



##### 1.5.2.2 功能测试

代码编写完毕，我们重新启动服务器，进行测试，可以通过debug断点跟踪的形式查看页面传递的参数封装情况，及套餐相关数据的保存情况。

录入表单数据: 

![image-20210806014328575](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806014328575.png) 

debug跟踪数据封装:

![image-20210806014508310](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806014508310.png) 

跟踪数据库保存的数据:

![image-20210806014807017](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806014807017.png) 





## 2. 套餐分页查询

### 2.1 需求分析

系统中的套餐数据很多的时候，如果在一个页面中全部展示出来会显得比较乱，不便于查看，所以一般的系统中都会以分页的方式来展示列表数据。

![image-20210806073710653](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806073710653.png) 

在进行套餐数据的分页查询时，除了传递分页参数以外，还可以传递一个可选的条件(套餐名称)。查询返回的字段中，包含套餐的基本信息之外，还有一个套餐的分类名称，在查询时，需要关联查询这个字段。



### 2.2 前端页面分析

在开发代码之前，需要梳理一下套餐分页查询时前端页面和服务端的交互过程：

1). 访问页面(backend/page/combo/list.html)，页面加载时，会自动发送ajax请求，将分页查询参数(page、pageSize、name)提交到服务端，获取分页数据

<img src="../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806074846550.png" alt="image-20210806074846550" style="zoom:80%;" /> 



2). 在列表渲染展示时，页面发送请求，请求服务端进行图片下载，用于页面图片展示(**已实现**)

![image-20210806075231072](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806075231072.png) 



而对于以上的流程中涉及到2个功能,文件下载功能我们已经实现,本小节我们主要实现列表分页查询功能, 具体的请求信息如下:

| 请求     | 说明                         |
| -------- | ---------------------------- |
| 请求方式 | GET                          |
| 请求路径 | /setmeal/page                |
| 请求参数 | ?page=1&pageSize=10&name=xxx |



### 2.3 代码开发

#### 2.3.1 基本信息查询

上述我们已经分析列表分页查询功能的请求信息，接下来我们就在SetmealController中创建套餐分页查询方法。

该方法的逻辑如下： 

1). 构建分页条件对象

2). 构建查询条件对象，如果传递了套餐名称，根据套餐名称模糊查询， 并对结果按修改时间降序排序

3). 执行分页查询

4). 组装数据并返回

代码实现 : 

```java
/**
  * 套餐分页查询
  * @param page
  * @param pageSize
  * @param name
  * @return
  */
@GetMapping("/page")
public R<Page> page(int page,int pageSize,String name){
    //分页构造器对象
    Page<Setmeal> pageInfo = new Page<>(page,pageSize);
	
    LambdaQueryWrapper<Setmeal> queryWrapper = new LambdaQueryWrapper<>();
    //添加查询条件，根据name进行like模糊查询
    queryWrapper.like(name != null,Setmeal::getName,name);
    //添加排序条件，根据更新时间降序排列
    queryWrapper.orderByDesc(Setmeal::getUpdateTime);

    setmealService.page(pageInfo,queryWrapper);
    return R.success(pageInfo);
}
```



#### 2.3.2 问题分析

基本分页查询代码编写完毕后，重启服务，测试列表查询，我们发现, 列表页面的数据可以展示出来, 但是套餐分类名称没有展示出来。

![image-20210806082542473](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806082542473.png) 

这是因为在服务端仅返回分类ID(categoryId), 而页面展示需要的是categoryName属性。 



#### 2.3.3 功能完善

在查询套餐信息时, 只包含套餐的基本信息, 并不包含套餐的分类名称, 所以在这里查询到套餐的基本信息后, 还需要根据分类ID(categoryId), 查询套餐分类名称(categoryName)，并最终将套餐的基本信息及分类名称信息封装到SetmealDto(在第一小节已经导入)中。

```java
@Data
public class SetmealDto extends Setmeal {
    private List<SetmealDish> setmealDishes; //套餐关联菜品列表
    private String categoryName;//套餐分类名称
}
```



完善后代码: 

```java
/**
* 套餐分页查询
* @param page
* @param pageSize
* @param name
* @return
*/
@GetMapping("/page")
public R<Page> page(int page,int pageSize,String name){
    //分页构造器对象
    Page<Setmeal> pageInfo = new Page<>(page,pageSize);
    Page<SetmealDto> dtoPage = new Page<>();

    LambdaQueryWrapper<Setmeal> queryWrapper = new LambdaQueryWrapper<>();
    //添加查询条件，根据name进行like模糊查询
    queryWrapper.like(name != null,Setmeal::getName,name);
    //添加排序条件，根据更新时间降序排列
    queryWrapper.orderByDesc(Setmeal::getUpdateTime);

    setmealService.page(pageInfo,queryWrapper);

    //对象拷贝
    BeanUtils.copyProperties(pageInfo,dtoPage,"records");
    List<Setmeal> records = pageInfo.getRecords();

    List<SetmealDto> list = records.stream().map((item) -> {
        SetmealDto setmealDto = new SetmealDto();
        //对象拷贝
        BeanUtils.copyProperties(item,setmealDto);
        //分类id
        Long categoryId = item.getCategoryId();
        //根据分类id查询分类对象
        Category category = categoryService.getById(categoryId);
        if(category != null){
            //分类名称
            String categoryName = category.getName();
            setmealDto.setCategoryName(categoryName);
        }
        return setmealDto;
    }).collect(Collectors.toList());

    dtoPage.setRecords(list);
    return R.success(dtoPage);
}
```



### 2.4 功能测试

代码完善后，重启服务，测试列表查询，我们发现, 抓取浏览器的请求响应数据，我们可以获取到套餐分类名称categoryName，也可以在列表页面展示出来 。

![image-20210806083346578](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806083346578.png) 



### 2.4 功能测试

代码完善后，重启服务，测试列表查询，我们发现, 抓取浏览器的请求响应数据，我们可以获取到套餐分类名称categoryName，也可以在列表页面展示出来 。

![image-20210806083346578](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806083346578.png) 





## 3. 删除套餐

### 3.1 需求分析

在套餐管理列表页面,点击删除按钮，可以删除对应的套餐信息。也可以通过复选框选择多个套餐，点击批量删除按钮一次删除多个套餐。注意，对于状态为售卖中的套餐不能删除，需要先停售，然后才能删除。

![image-20210806214443507](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806214443507.png) 



### 3.2 前端页面分析

在开发代码之前，需要梳理一下删除套餐时前端页面和服务端的交互过程：

1). 点击删除, 删除单个套餐时，页面发送ajax请求，根据套餐id删除对应套餐

![image-20210806215911878](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806215911878.png) 

2). 删除多个套餐时，页面发送ajax请求，根据提交的多个套餐id删除对应套餐

![image-20210806220406587](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806220406587.png) 



开发删除套餐功能，其实就是在服务端编写代码去处理前端页面发送的这2次请求即可，一次请求为根据ID删除，一次请求为根据ID批量删除。

观察删除单个套餐和批量删除套餐的请求信息可以发现，两种请求的**地址**和**请求方式**都是相同的，不同的则是传递的id个数，所以在服务端可以提供一个方法来统一处理。

具体的请求信息如下： 

具体的请求信息如下： 

| 请求     | 说明                                         |
| -------- | -------------------------------------------- |
| 请求方式 | DELETE                                       |
| 请求路径 | /setmeal                                     |
| 请求参数 | ?ids=1423640210125656065,1423338765002256385 |



### 3.3 代码开发

删除套餐的流程及请求信息，我们分析完毕之后，就来完成服务端的逻辑开发。在服务端的逻辑中, 删除套餐时, 我们不仅要删除套餐, 还要删除套餐与菜品的关联关系。



**1). 在SetmealController中创建delete方法**

我们可以先测试在delete方法中接收页面提交的参数，具体逻辑后续再完善：



编写完代码，我们重启服务之后，访问套餐列表页面，勾选复选框，然后点击"批量删除",我们可以看到服务端可以接收到集合参数ids，并且在控制台也可以输出对应的数据 。

![image-20210806221603303](../../../瑞吉外卖和springboot资料/04-讲义/瑞吉外卖-day05/assets/image-20210806221603303.png) 



**2). SetmealService接口定义方法removeWithDish**

```java
/**
 * 删除套餐，同时需要删除套餐和菜品的关联数据
 * @param ids
 */
public void removeWithDish(List<Long> ids);
```

