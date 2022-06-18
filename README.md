### family_service_platform

# 项目开发流程

### 1、根据需求文档分析整个项目，进行相关表的设计

​		通过需求文档分析各个模块需要的表以及相应的表之间的关系，此阶段主要是想让大家熟悉这个过程，后续实际开发的时候我会将表结构直接给出，大家做对比即可，记住一句话，**表设计没有对错之分，只有合适与不合适**

### 2、搭建前端项目

​		此项目的开发我们使用的是阿里开源的前端框架Ant Design,此框架是使用vue来完成具体功能的，因为在现在的公司的开发中，几乎都是前后端分离，前端工程师完成前端功能，后端工程师完成后端逻辑的编写，我们教学的侧重点在于后端，因此前端直接使用给大家提供好的模板，大家只需要下载即可。

操作步骤：

​		1、下载项目并完成解压的功能

​		2、在当前项目的根路径下打开cmd，然后运行npm install

​		3、在整个安装过程中一般不会出现任何错误，如果出现错误，自行上网解决，解决不了联系我，(别怂，干就完了)。

​		4、安装完成之后，可以使用 npm run serve命令来进行 运行

### 3、搭建后端项目

​		后端我们抛弃使用ssm的这种老的项目搭建模式，使用现在应用最多的springboot来进行实现。

操作步骤：

​		1、创建springboot项目

​		2、导入需要的依赖


​		3、修改配置文件

​		4、通过mybatis-plus反向生成对应的实体类


​		5、运行springboot项目，保证项目能够运行起来。	

​		6、运行之后发现项目报错，报错原因是因为mapper对象无法自动装配，因此需要在启动的application类上添加@MapperScan注解或者在每一个mapper的接口上添加@Mapper注解，此处不做任何限制，大家开心就好。当然不可否认的是我们当前项目的表比较多，因此在每一个Mapper上添加@Mapper注解比较麻烦

***

​		总结，截止到此处为止，我们需要环境准备工作已经完成，下面开始进行下一步的开发。

​		因为现在大家在访问的时候用到的是两个独立的项目，所以必须要保证两个项目之间能够进行通信，此时对于前端和后端而言，我们都需要进行相关的配置，来保证两个项目可以进行正常的数据通信

### 4、修改前端项目配置，完成通信功能

​		1、修改.env.development文件

```tex
NODE_ENV=development
VUE_APP_PREVIEW=false
VUE_APP_API_BASE_URL=http://localhost:8080/
```

​		2、删除main.js的mock数据挂载，在src目录下打开main.js文件，将import './mock'代码注释

​		3、当修改成功之后，重新运行服务，点击登录请求，查看发送的地址是什么地址，如果出现http://localhost:8080/auth/login，则表示前端项目的修改已经成功，只需要去修改后端项目的配置即可。不要问我不成功怎么办，你按照我的方式，不可能不成功！

### 5、修改后端项目的配置，完成通信功能

​		1、在项目中新建一个config的包，添加跨域的配置类

```java
package com.mashibing.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@Configuration
public class CorsConfig {
    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        //  你需要跨域的地址  注意这里的 127.0.0.1 != localhost
        // * 表示对所有的地址都可以访问
        corsConfiguration.addAllowedOrigin("*");
        //  跨域的请求头
        corsConfiguration.addAllowedHeader("*"); // 2
        //  跨域的请求方法
        corsConfiguration.addAllowedMethod("*"); // 3
        //加上了这一句，大致意思是可以携带 cookie
        //最终的结果是可以 在跨域请求的时候获取同一个 session
        corsConfiguration.setAllowCredentials(true);
        return corsConfiguration;
    }
    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        //配置 可以访问的地址
        source.registerCorsConfiguration("/**", buildConfig()); // 4
        return new CorsFilter(source);
    }
}
```

​		或者可以在controller类的上面添加注解

```java
@CrossOrigin(origins = "*",allowCredentials="true",allowedHeaders = "*",methods = {})
```

​		2、添加对应的controller进行处理

LoginController.java

```java
package com.bjmsb.controller;

import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@CrossOrigin(origins = "*",allowCredentials="true",allowedHeaders = "*",methods = {})
public class LoginController {

    @RequestMapping("/auth/2step-code")
    public boolean step_code2(){
        System.out.println("此请求是前端框架带的默认请求，可以不做任何处理，也可以在前端将其删除");
        System.out.println("step_code2");
        return true;
    }

    @RequestMapping("/auth/login")
    public String login(){
        System.out.println("login");
        return "success";
    }
}
```

如果能请求成功，那么就意味着整个项目已经可以顺利进行通信，后续的话只需要完成对应业务的编写即可，

***

​		从下面开始，我们就要开始进行整个项目的业务开发了，所以希望大家能提起精神，干他！！！
