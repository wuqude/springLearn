# 瑞吉外卖-第六天

## 课程内容

- 用户地址簿功能
- 菜品展示
- 购物车
- 下单







## 1. 用户地址簿功能

### 1.1 需求分析

地址簿，指的是移动端消费者用户的地址信息，用户登录成功后可以维护自己的地址信息。同一个用户可以有多个地址信息，但是只能有一个**默认地址**。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812191332892.png" alt="image-20210812191332892" style="zoom: 67%;" />  

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812191822693.png" alt="image-20210812191822693" style="zoom:67%;" /> 

对于地址簿管理，我们需要实现以下几个功能： 

- 新增地址
- 地址列表查询
- 设置默认地址
- 编辑地址
- 删除地址



### 1.2 数据模型

用户的地址信息会存储在address_book表，即地址簿表中。具体表结构如下：

![image-20210812192228678](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812192228678.png) 

这里面有一个字段is_default，实际上我们在设置默认地址时，只需要更新这个字段就可以了。

### 1.3 导入功能代码

对于这一类的单表的增删改查，我们已经写过很多了，基本的开发思路都是一样的，那么本小节的用户地址簿管理的增删改查功能，我们就不再一一实现了，基本的代码我们都已经提供了，直接导入进来，做一个测试即可。

对于下面的地址管理的代码，我们可以直接从资料拷贝，也可以直接从下面的讲义中复制。



**1). 实体类 AddressBook（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.entity

```java
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.TableField;
import lombok.Data;
import java.io.Serializable;
import java.time.LocalDateTime;
/**
 * 地址簿
 */
@Data
public class AddressBook implements Serializable {
    private static final long serialVersionUID = 1L;

    private Long id;
	
    //用户id
    private Long userId;
	
    //收货人
    private String consignee;
	
    //手机号
    private String phone;
	
    //性别 0 女 1 男
    private String sex;
	
    //省级区划编号
    private String provinceCode;
	
    //省级名称
    private String provinceName;
	
    //市级区划编号
    private String cityCode;
	
    //市级名称
    private String cityName;
	
    //区级区划编号
    private String districtCode;
	
    //区级名称
    private String districtName;
	
    //详细地址
    private String detail;
	
    //标签
    private String label;
    
    //是否默认 0 否 1是
    private Integer isDefault;
    
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

    //是否删除
    private Integer isDeleted;
}
```



**2). Mapper接口 AddressBookMapper（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.mapper

```java
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.reggie.entity.AddressBook;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;

@Mapper
public interface AddressBookMapper extends BaseMapper<AddressBook> {
}
```



**3). 业务层接口 AddressBookService（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.service

```java
import com.baomidou.mybatisplus.extension.service.IService;
import com.itheima.reggie.entity.AddressBook;

public interface AddressBookService extends IService<AddressBook> {
}
```

**4). 业务层实现类 AddressBookServiceImpl（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.service.impl

```java
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.itheima.reggie.entity.AddressBook;
import com.itheima.reggie.mapper.AddressBookMapper;
import com.itheima.reggie.service.AddressBookService;
import org.springframework.stereotype.Service;

@Service
public class AddressBookServiceImpl extends ServiceImpl<AddressBookMapper, AddressBook> implements AddressBookService {
}
```



**5). 控制层 AddressBookController（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.controller



controller主要开发的功能: 

A. 新增地址逻辑说明： 

- 需要记录当前是哪个用户的地址(关联当前登录用户)

B. 设置默认地址 

- 每个用户可以有很多地址，但是默认地址只能有一个 ；

- 先将该用户所有地址的is_default更新为0 , 然后将当前的设置的默认地址的is_default设置为1

C. 根据ID查询地址

D. 查询默认地址

- 根据当前登录用户ID 以及 is_default进行查询，查询当前登录用户is_default为1的地址信息

E. 查询指定用户的全部地址

- 根据当前登录用户ID，查询所有的地址列表



代码实现如下： 

```java
import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.LambdaUpdateWrapper;
import com.itheima.reggie.common.BaseContext;
import com.itheima.reggie.common.R;
import com.itheima.reggie.entity.AddressBook;
import com.itheima.reggie.service.AddressBookService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;

/**
 * 地址簿管理
 */
@Slf4j
@RestController
@RequestMapping("/addressBook")
public class AddressBookController {
    @Autowired
    private AddressBookService addressBookService;

    /**
     * 新增
     */
    @PostMapping
    public R<AddressBook> save(@RequestBody AddressBook addressBook) {
        addressBook.setUserId(BaseContext.getCurrentId());
        log.info("addressBook:{}", addressBook);
        addressBookService.save(addressBook);
        return R.success(addressBook);
    }

    /**
     * 设置默认地址
     */
    @PutMapping("default")
    public R<AddressBook> setDefault(@RequestBody AddressBook addressBook) {
        log.info("addressBook:{}", addressBook);
        LambdaUpdateWrapper<AddressBook> wrapper = new LambdaUpdateWrapper<>();
        wrapper.eq(AddressBook::getUserId, BaseContext.getCurrentId());
        wrapper.set(AddressBook::getIsDefault, 0);
        //SQL:update address_book set is_default = 0 where user_id = ?
        addressBookService.update(wrapper);

        addressBook.setIsDefault(1);
        //SQL:update address_book set is_default = 1 where id = ?
        addressBookService.updateById(addressBook);
        return R.success(addressBook);
    }

    /**
     * 根据id查询地址
     */
    @GetMapping("/{id}")
    public R get(@PathVariable Long id) {
        AddressBook addressBook = addressBookService.getById(id);
        if (addressBook != null) {
            return R.success(addressBook);
        } else {
            return R.error("没有找到该对象");
        }
    }

    /**
     * 查询默认地址
     */
    @GetMapping("default")
    public R<AddressBook> getDefault() {
        LambdaQueryWrapper<AddressBook> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(AddressBook::getUserId, BaseContext.getCurrentId());
        queryWrapper.eq(AddressBook::getIsDefault, 1);

        //SQL:select * from address_book where user_id = ? and is_default = 1
        AddressBook addressBook = addressBookService.getOne(queryWrapper);

        if (null == addressBook) {
            return R.error("没有找到该对象");
        } else {
            return R.success(addressBook);
        }
    }

    /**
     * 查询指定用户的全部地址
     */
    @GetMapping("/list")
    public R<List<AddressBook>> list(AddressBook addressBook) {
        addressBook.setUserId(BaseContext.getCurrentId());
        log.info("addressBook:{}", addressBook);

        //条件构造器
        LambdaQueryWrapper<AddressBook> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(null != addressBook.getUserId(), AddressBook::getUserId, addressBook.getUserId());
        queryWrapper.orderByDesc(AddressBook::getUpdateTime);

        //SQL:select * from address_book where user_id = ? order by update_time desc
        return R.success(addressBookService.list(queryWrapper));
    }
}
```



### 1.4 功能测试

代码导入进来，并且去阅读了一下地址管理各个功能的逻辑实现，接下来，我们就可以启动项目，进行一个测试。测试过程中，通过debug断点调试观察服务端程序的执行过程，在浏览器中使用调试工具查看页面和服务端的交互过程和请求响应数据。

**1). 新增**

填写表单数据，点击保存地址，查看网络请求。

![image-20210812201636567](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812201636567.png) 

测试完毕之后，检查数据库中的数据，是否正常插入。

![image-20210812201845336](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812201845336.png) 



**2). 列表查询**

当新增地址完成后，页面会再次发送一个请求，来查询该用户的所有地址列表，在界面进行展示。

![image-20210812202430677](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812202430677.png) 

![image-20210812202534169](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812202534169.png) 



**3). 设置默认**

在地址列表页面中，勾选 "设为默认地址" ，此时会发送PUT请求，来设置默认地址。

![image-20210812202841250](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812202841250.png) 

测试完毕后，我们再次查看数据库表中的数据：

![image-20210812203123499](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812203123499.png) 

## 2. 菜品展示

### 2.1 需求分析

用户登录成功后跳转到系统首页，在首页需要根据分类来展示菜品和套餐。如果菜品设置了口味信息，需要展示<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812205330291.png" alt="image-20210812205330291" style="zoom:80%;" />按钮，否则显示<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812205346846.png" alt="image-20210812205346846" style="zoom:80%;" />按钮。

![image-20210812210328249](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812210328249.png) 



### 2.2 前端页面分析

在开发代码之前，需要梳理一下前端页面和服务端的交互过程：



**1). 页面(front/index.html)发送ajax请求，获取分类数据（菜品分类和套餐分类）**

![image-20210812215624794](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812215624794.png) 

该功能在之前的业务开发中，我们都已经实现了。通过请求响应的数据，我们也可以看到数据是可以正确获取到的。

![image-20210812221107947](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812221107947.png) 





注意：首页加载时，不仅发送请求获取分类列表，还发送了一次ajax请求用于加载购物车数据，而这两次请求必须全部成功，页面才可以正常渲染，而当前购物车列表查询功能还未实现(报404)，所以列表目前并未渲染。此处可以将这次请求的地址暂时修改一下，从静态json文件获取数据，等后续开发购物车功能时再修改回来，如下：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812221835628.png" alt="image-20210812221835628" style="zoom:80%;" /> 

修改之后，我们再次测试：  

![image-20210812222713700](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812222713700.png) 

目前该部分的功能我们已经调通，左侧的分类菜单，和右侧的菜品信息我们都可以看到，后续我们只需要将购物车列表的数据改成调用服务端接口查询即可。



**2). 页面发送ajax请求，获取第一个分类下的菜品或者套餐**

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812224353891.png" alt="image-20210812224353891" style="zoom:80%;" /> 

A. 根据分类ID查询套餐列表：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812224810551.png" alt="image-20210812224810551" style="zoom:80%;" /> 

B. 根据分类ID查询菜品列表： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812224624459.png" alt="image-20210812224624459" style="zoom:80%;" /> 

异步请求，查询分类对应的菜品列表，功能我们已经实现了，但是我们之前查询的只是菜品的基本信息，不包含菜品的口味信息。所以在前端界面中，我们看不到选择菜品分类的信息。

![image-20210812231220115](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812231220115.png) 

经过上述的分析，我们可以看到，服务端我们主要提供两个方法， 分别用来：

A. 根据分类ID查询菜品列表(包含菜品口味列表), 具体请求信息如下: 

| 请求     | 说明                                     |
| -------- | ---------------------------------------- |
| 请求方式 | GET                                      |
| 请求路径 | /dish/list                               |
| 请求参数 | ?categoryId=1397844263642378242&status=1 |

<font color='red'>该功能在服务端已经实现，我们需要修改此方法，在原有方法的基础上增加查询菜品的口味信息。</font>



B. 根据分类ID查询套餐列表, 具体请求信息如下: 

| 请求     | 说明                                     |
| -------- | ---------------------------------------- |
| 请求方式 | GET                                      |
| 请求路径 | /setmeal/list                            |
| 请求参数 | ?categoryId=1397844263642378242&status=1 |

<font color='red'>该功能在服务端并未实现。</font>



### 2.3 代码开发

#### 2.3.1 查询菜品方法修改

由于之前我们实现的根据分类查询菜品列表，仅仅查询了菜品的基本信息，未查询菜品口味信息，而移动端用户在点餐时，是需要选择口味信息的，所以我们需要对之前的代码实现进行完善，那么如何完善呢？

我们需要修改DishController的list方法，原来此方法的返回值类型为：R<List<Dish>>。为了满足移动端对数据的要求(菜品基本信息和菜品对应的口味信息)，现在需要将方法的返回值类型改为：R<List<DishDto>> ，因为在DishDto中封装了菜品对应的口味信息： 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210812231825043.png" alt="image-20210812231825043" style="zoom:67%;" /> 

**代码逻辑:** 

A. 根据分类ID查询，查询目前正在启售的菜品列表 (已实现)

B. 遍历菜品列表，并查询菜品的分类信息及菜品的口味列表

C. 组装数据DishDto，并返回



**代码实现：**

```java
@GetMapping("/list")
    public R<List<DishDto>> list(Dish dish){
        //构造查询条件
        LambdaQueryWrapper<Dish> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(dish.getCategoryId() != null ,Dish::getCategoryId,dish.getCategoryId());
        //添加条件，查询状态为1（起售状态）的菜品
        queryWrapper.eq(Dish::getStatus,1);
        //添加排序条件
        queryWrapper.orderByAsc(Dish::getSort).orderByDesc(Dish::getUpdateTime);
		
        List<Dish> list = dishService.list(queryWrapper);
	
        List<DishDto> dishDtoList = list.stream().map((item) -> {
            DishDto dishDto = new DishDto();
            BeanUtils.copyProperties(item,dishDto);

            Long categoryId = item.getCategoryId();//分类id
            //根据id查询分类对象
            Category category = categoryService.getById(categoryId);
            if(category != null){
                String categoryName = category.getName();
                dishDto.setCategoryName(categoryName);
            }

            //当前菜品的id
            Long dishId = item.getId();
            LambdaQueryWrapper<DishFlavor> lambdaQueryWrapper = new LambdaQueryWrapper<>();
            lambdaQueryWrapper.eq(DishFlavor::getDishId,dishId);
            //SQL:select * from dish_flavor where dish_id = ?
            List<DishFlavor> dishFlavorList = dishFlavorService.list(lambdaQueryWrapper);
            dishDto.setFlavors(dishFlavorList);
            
            return dishDto;
        }).collect(Collectors.toList());

        return R.success(dishDtoList);
    }
```



#### 2.3.2 根据分类ID查询套餐



### 2.4 功能测试

把菜品展示的功能代码完善完成之后，我们重新启动服务，来测试一个菜品展示的功能。测试过程中可以使用浏览器的监控工具查看页面和服务端的数据交互细节。

![image-20210813175554516](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813175554516.png) 

点击分类，根据分类查询菜品列表/套餐列表:

![image-20210813175835304](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813175835304.png) 





## 3. 购物车

### 3.1 需求分析

移动端用户可以将菜品或者套餐添加到购物车。对于菜品来说，如果设置了口味信息，则需要选择规格后才能加入购物车;对于套餐来说，可以直接点击<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813181916235.png" alt="image-20210813181916235" style="zoom:67%;" />将当前套餐加入购物车。在购物车中可以修改菜品和套餐的数量，也可以清空购物车。

![image-20210813182828045](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813182828045.png) 

这里面我们需要实现的功能包括: 

1). 添加购物车

2). 查询购物车

3). 清空购物车



### 3.2 数据模型

用户的购物车数据，也是需要保存在数据库中的，购物车对应的数据表为shopping_cart表，具体表结构如下：

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813183334933.png" alt="image-20210813183334933" style="zoom:80%;" /> 

说明： 

- 购物车数据是关联用户的，在表结构中，我们需要记录，每一个用户的购物车数据是哪些
- 菜品列表展示出来的既有套餐，又有菜品，如果APP端选择的是套餐，就保存套餐ID(setmeal_id)，如果APP端选择的是菜品，就保存菜品ID(dish_id)
- 对同一个菜品/套餐，如果选择多份不需要添加多条记录，增加数量number即可



**最终shopping_cart表中存储的数据示例:**

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210815183440051.png" alt="image-20210815183440051" style="zoom:80%;" /> 





### 3.3 前端页面分析

在开发代码之前，需要梳理一下购物车操作时前端页面和服务端的交互过程：



1). 点击 "加入购物车" 或者 "+" 按钮，页面发送ajax请求，请求服务端，将菜品或者套餐添加到购物车

![image-20210813185414102](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813185414102.png) 

![image-20210813185731809](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813185731809.png) 



2). 点击购物车图标，页面发送ajax请求，请求服务端查询购物车中的菜品和套餐

此时，我们就需要将查询购物车的代码放开，不用再加载静态的json数据了。

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813190814766.png" alt="image-20210813190814766" style="zoom:80%;" /> 

<img src="../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813191237556.png" alt="image-20210813191237556" style="zoom:70%;" /> 



3). 点击清空购物车按钮，页面发送ajax请求，请求服务端来执行清空购物车操作

![image-20210813192012994](../../../Java资料/1 瑞吉外卖项目/04-讲义/瑞吉外卖-day06/assets/image-20210813192012994.png) 





经过上述的分析，我们可以看到，对于购物车的功能，我们主要需要开发以下几个功能，具体的请求信息如下：

**1). 加入购物车**

| 请求     | 说明              |
| -------- | ----------------- |
| 请求方式 | POST              |
| 请求路径 | /shoppingCart/add |
| 请求参数 | json格式          |

```json
菜品数据: 
{"amount":118,"dishFlavor":"不要蒜,微辣","dishId":"1397851099502260226","name":"全家福","image":"a53a4e6a-3b83-4044-87f9-9d49b30a8fdc.jpg"}

套餐数据: 
{"amount":38,"setmealId":"1423329486060957698","name":"营养超值工作餐","image":"9cd7a80a-da54-4f46-bf33-af3576514cec.jpg"}
```





**2). 查询购物车列表**

| 请求     | 说明               |
| -------- | ------------------ |
| 请求方式 | GET                |
| 请求路径 | /shoppingCart/list |



**3). 清空购物车功能**

| 请求     | 说明                |
| -------- | ------------------- |
| 请求方式 | DELETE              |
| 请求路径 | /shoppingCart/clean |





### 3.4 准备工作

分析完毕购物车的业务需求和实现思路之后，在开发业务功能前，先将需要用到的类和接口基本结构创建好：

**1). 实体类 ShoppingCart（直接从课程资料中导入即可）**

所属包: com.itheima.reggie.entity

```java
import lombok.Data;
import java.io.Serializable;
import java.math.BigDecimal;
import java.time.LocalDateTime;

/**
 * 购物车
 */
@Data
public class ShoppingCart implements Serializable {
    private static final long serialVersionUID = 1L;
	
    private Long id;

    //名称
    private String name;

    //用户id
    private Long userId;

    //菜品id
    private Long dishId;

    //套餐id
    private Long setmealId;

    //口味
    private String dishFlavor;

    //数量
    private Integer number;

    //金额
    private BigDecimal amount;

    //图片
    private String image;

    private LocalDateTime createTime;
}
```



**2). Mapper接口 ShoppingCartMapper**

所属包: com.itheima.reggie.mapper

```java
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.reggie.entity.ShoppingCart;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface ShoppingCartMapper extends BaseMapper<ShoppingCart> {
}
```

### 3.5 代码开发

#### 3.5.1 添加购物车

在ShoppingCartController中创建add方法，来完成添加购物车的逻辑实现，具体的逻辑如下： 

A. 获取当前登录用户，为购物车对象赋值

B. 根据当前登录用户ID 及 本次添加的菜品ID/套餐ID，查询购物车数据是否存在

C. 如果已经存在，就在原来数量基础上加1

D. 如果不存在，则添加到购物车，数量默认就是1



代码实现如下：

#### 3.5.2 查询购物车

在ShoppingCartController中创建list方法，根据当前登录用户ID查询购物车列表，并对查询的结果进行创建时间的倒序排序。

代码实现如下： 
