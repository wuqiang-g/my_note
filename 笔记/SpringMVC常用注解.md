# 1. Controller方法中参数注解

## 1.1 @RequestParam

获取URL字符串中的参数，例如xxx/yyy?id=10 ; id就是该注解可以获取到的参数，一个方法中可以有多个@RequestParam注解



## 1.2 @PathVariable

获取URL字符串中占位符所代表的字段，例如xxx/yyy/{id}/{code} ;id 和 code字段在方法中都用@PathVariable注解修饰



## 1.3 @RequestBody

获取http reuqestbody 中的参数（一般前端传过来一个JSON对象，由SpringMVC负责转换成Java对象），由于get请求没有请求体，**所以这一种必定是post请求**，用@RequestBody修饰的java对象可以是普通Bean也可以是数组或者列表；前端post请求，请求头需要加上（或者设置）

Content-Type：application/json 才可以，因为@RequestBody默认接收的enctype (MIME编码)是application/json。@RequestBody在一个方法中只能有一个。

需要注意到的是：如果前端传的是一个JSON数组作为请求体，后段必须用List接收，不要用对象数组；



## 1.4 前端各种请求与后端对应的接收

### 1.4.1 get 请求

  xxx/yyy?id=15&code=1000

（Long id,@RequestParam("code") Long tCode）/ (RequestReq req) req包括id和code两个字段

1.  前端的参数可以直接通过&分割开，往后端传，后端可以不加@RequestParam接收，前提前端的字段与后端的字段名字一样；

​    如果前端传的字段比较多，后端可以方法中参数可以封装一个对象，springmvc会自动映射对象中的字段



  xxx/yyy/15/1000

  @GetMapper(/xxx/yyy/{id}/{code})

（@PathVariable Long id,@PathVariable Long code）

前端通过/分割参数，后端通过占位符分割，@PathVariable获取



### 1.4.2 Post请求

Post请求往后端发送的数据放在Http Request的请求体里面，发送的数据需要进行格式化，常见的格式化类型如下几种

-   **Content-Type : application/x-www-form-urlencoded**

这种是最常见的post提交数据格式，原生表单不改的话，用的就是这种格式；再有ajax提交时，像 JQuery 和 QWrap都是默认用这种格式，这种格式基于**key-value**的形式提交数据。会将表单的数据合成类似于id=1&name=ww&age=23 这样的很长的字符串存入body里面。



-   **Content-Type : application/****json**

这种目前越来越流行，向后端提供的是JSON格式的字符串，json支持比键值对更复杂的数据结构；这种格式化提交数据，需要后端给参数加上@RequestBody注解。



-   **Content-Type ：** **multipart/form-data**

这种格式一般用于向服务端上传文件，也是表单原生支持的。



# 2.Controller层常用的注解

## 2.1 @Controller

JavaBean注解，spring加载Bean时，会将带有javaBean注解的类单例化存入ioc容器里面，其他JavaBean注解还有@Service , @Repository(对应DAO层,RepoImpl常加上它),@Component(spring通用JavaBean注解，像Manager层就用这个),@Mapper等注解；



## 2.2 @RequestMapping

用来描述该Controller类或者里面的方法的访问路径



## 2.3 ResponseBody

可以单独加载方法上，也可以加载类上，代表类里面的所以方法都加上了。

主要作用：将方法的返回值按照前端请求返回格式格式化，然后**直接写入HttpServletResponse响应体中**，而不再走SpringMVC的试图处理器。



## 2.4 RestController(组合注解)

相当于Controller + ResponseBody 放在类上面