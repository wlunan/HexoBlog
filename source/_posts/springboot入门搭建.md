# :rocket:SpringBoot入门搭建

基于IDEA开发

## 1.创建空项目

![image-20220628162812943](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220628162812943.png)

## 2.创建新模块，选择Spring Initializr，并配置模块相关基础信息

默认下载源：https://start.spring.io

默认下载源由于在国外，有时会无法使用，那么可以选择使用国内阿里源：[阿里云知行动手实验室-在浏览器沉浸式学习最新云原生技术 (aliyun.com)](https://start.aliyun.com/)

![image-20220628163105544](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220628163105544.png)



## 3.选择当前模块需要使用的技术集

springboot版本可以在创建后的pom.xml文件中更改

## ![image-20220628163352399](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220628163352399.png)



## 4.开发控制器类

测试类

```java
//Rest模式
@RestController
@RequestMapping("/books")
public class BookController {
    @GetMapping
    public String getById() {
        System.out.println("springboot is running...");
        return "springboot is running...";
    }
}
```

## 5.测试结果

![image-20220628164123806](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220628164123806.png)![image-20220628164204027](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220628164204027.png)