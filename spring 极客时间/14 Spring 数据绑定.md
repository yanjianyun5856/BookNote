

####  Spring 数据绑定组件

标准组件

​	`org.springframework.validation.DataBinder`

Web组件

​	`org.springframework.web.bind.WebDataBinder`

​	`org.springframework.web.bind.ServletReuestDataBinder`

​	`org.springframework.web.bind.support.WebRequestDataBinder`

​	`org.springframework.web.bind.support.WebChangeDataBinder`



##### DataBinder 核心属性

1. target：关联目标Bean
2. objectName：目标bean名称
3. bindingResult：属性绑定结果
4. typeConverter：类型转换器
5. conversionService：类型转换服务
6. messageCodesResolver：校验错误文案处理器
7. validators：关联的 Bean Validator 实例集合

#####  DateBinder绑定方法

bind(PropertyValues)：将 PropertyValues Key-Value 内容映射到关联Bean(target)中的属性上



####Spring 数据绑定元数据

DataBinder 元数据 - PropertyValues

| 特征         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| 数据来源     | BeanDefinition，主要来源XML资源配置BeanDefinition            |
| 数据结构     | 由一个或多个PropertyValue组成                                |
| 成员结构     | PropertyValue包含属性名称，以及属性值                        |
| 常见实现     | MutablePropertyValues                                        |
| Web扩展实现  | ServletConfigPropertyValues、ServletRequestParameterPropertyValues |
| 相关声明周期 | InstantiationAwareBeanPostProcessor#postProcessProperties    |



####  DataBinder 控制参数

参数：

1. `ignoreUnknownFields` : 是否忽略未知字段，默认true
2. `ignoreInvalidFields` : 是否忽略非法字段，默认false
3. `autoGrowNestedPaths` : 是否自动增加嵌套路径，默认true
4. `String[] allowedFields` : 绑定字段白名单
5. `String[] disallowedFields` : 绑定字段黑名单
6. `String[] requiredFields` : 必须绑定字段

```java
public class DataBinderDemo {
    public static void main(String[] args) {
        //创建空白对象
        User user = new User();
        //1. 创建 DataBinder
        DataBinder binder = new DataBinder(user,"");

        //2. 创建 PropertyValues
        Map<String,Object> source = new HashMap<>();
        source.put("id",1);
        source.put("name","小马哥");

        //PropertyValues 存在， user 中不存在属性
        // DataBinder 特性1： 默认忽略未知的属性
        source.put("age",18);
        //设置为false 抛出异常  age字段不存在 于 user 类
        //binder.setIgnoreUnknownFields(false);


        //PropertyValues 中存在嵌套属性，比如 company.name
        // DataBinder 特性2： 默认支持嵌套属性
        source.put("company.name","KL");

        PropertyValues propertyValues = new MutablePropertyValues(source);

        binder.bind(propertyValues);

        //3. 输出user 内容
        System.out.println(user);

    }
}

```



####  Spring 底层java beans 替换实现

java beans核心实现 - java.beans.BeanInfo

​	属性（Property）

​	方法（Method）

​	事件（Event）

​	表达式（Expression）

Spring替代实现 - org.springframework.beans.BeanWrapper

​	属性（Property）

​	嵌套属性路径（nested path）



