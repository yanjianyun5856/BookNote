

####  Validator 接口设计

核心方法

​	supports(Class) ：校验目标类能否校验

​	validate(Object,Errors)：校验目标对象，并将校验失败的内容输出至Errors 对象

配套组件

​	错误收集器：org.springframework.validation.Errors

​	Validator工具类：org.springframework.validation.ValidationUtils







