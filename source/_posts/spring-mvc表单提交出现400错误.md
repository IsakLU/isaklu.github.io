---
title: spring-mvc表单提交出现400错误
date: 2017-05-08 17:11:01
tags: web框架
categories: java web

---

## 现象

​	jsp页面提交到Spring-mvc框架action之后 ，返回给浏览器400 Bad Request，后台是不报错误的，在浏览器的Console控制台打印出：

> jquery.easyui.min.js:5921 POST http://localhost:8080/scm/buyOrder/addBuyOrder.action 400 (Bad Request)



## 分析

​	400错误是指请求语法格式有误，服务器无法理解此请求。于是结合spring-mvc和jsp的ajax请求，得到以下几点原因：



- **最常见**的原因是spring-mvc中用于封装参数的javaBean中的某个属性类型和前台传递过来不匹配，例如javaBean中定义的Date对象，在jsp通过ajax传递过来的时候是字符串类型的，两者不匹配才会出现这个原因



- spring-mvc返回的json数据没有添加@ResponseBody注解



- jsp传递过来的参数包含了非javaBean的参数



- spring-mvc的controller的路径和jsp提交的路径不匹配



## 解决

​	解决方法主要是针对上面分析的第一种情况，其他情况都很容易检测出来并且修正。



1. 将实体javaBean中的类型转换为String类型，但是在写SQL的时候需要利用数据库函数的to_date或者to_number进行转换，同样从数据库中取出来转换为javaBean前需要转换为String类型，例如使用SimpleDateFormat进行转换。



2. 在javaBean中将这些非String的属性通过set方法转换为真实类型。

```java
public void setDate(String date) throws ParseException{
  SimpleDateFormate sdf = new SimpleDateFormate("yyyy-MM-dd HH:mm:ss");
  this.date = sdf.parse(date);
}
```



3. 在javaBean中非String的属性上添加注释 `@DateTimeFormat`、`@NumberFormat`

```
@NumberFormat(style=Style.CURRENCY)
private double money;
@DateTimeFormat(pattern="yyyy-MM-dd HH:mm:ss") 
 private Date date; 
```



## 参考

[springmvc form表单提交报400错误](http://blog.csdn.net/u014079773/article/details/51865178)

