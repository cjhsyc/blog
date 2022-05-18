---
title: springboot
date: 2022-05-03 22:37:32
categories: [springboot]
tags:
- springboot
- java
- mybatis
- druid
---

# 创建项目

idea创建Spring Initializr项目，自定项目名称，软件包名称改为com.example，java版本为8。

连不上start.spring.io，可以使用start.aliyun.com。（使用阿里云的服务器创建的springboot版本较低，可自行在pom.xml中修改）

依赖项选择Web下的Spring Web。

# 基本使用

`src/main/java`下新建`com.example.controller`包，新建java类：`BookController`：

```java
package com.example.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/books")
public class BookController {
    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id){
        return "running..."+id;
    }
}
```

@RestController：@Controller + @ResponseBody

@ResponseBody： 可以返回实体对象（返回json数据）

@PathVariable：使用路径参数

## 启动服务

执行`com.example`下的`Application`即可，springboot内嵌服务器（默认tomcat），端口：8080

# 基本配置

springboot的配置写在`resources`下的`application.properties`：

```properties
# 设置服务器端口
server.port = 80
```

除了properties格式还可以使用yml或yaml文件。

新建`application.yml`：

```yml
server:
  port: 80
```

新建`appalication.yaml`：

```yaml
server:
  port: 80
```

优先级：properties > yml > yaml

## yaml格式

yml和yaml文件都是yaml格式，这是一种数据序列化格式，以数据为中心，重数据轻格式。

读取yaml数据：

```java
@Value("${server.port}")
private Integer port;
```

变量引用：

```yml
lastname: wu
fullname: ${lastname}xian
```

如果属性值中出现转译字符，需要用双引号包裹：

```yml
lesson: "spring\nboot"
# \n : 换行
```

读取所有数据：

```java
//自动装配到Environment对象
@Autowired 
private Environment env;

//使用
env.getProperty("server.port")
```

读取数据到类中：

定义类：（Server）

```java
package com.example;

import org.springframework.stereotype.Component;

//定义为spring管控的类
@Component
//指定加载的数据
@ConfigurationProperties("server")
public class Server {
    //属性名与yml中对应
    private Integer port;
    
    // Alt+Ins 自动生成
    public Integer getPort() {
        return port;
    }

    public void setPort(Integer port) {
        this.port = port;
    }

    @Override
    public String toString() {
        return "Server{" +
                "port=" + port +
                '}';
    }
}
```

使用：

```java
@Autowired
private Server server;
```

# 整合Mybatis

新建项目，依赖项选择sql下的MyBatis Framework 和 MySQL Driver

使用：

`application.yml`:

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/books
    username: root
    password: "123456"
```

`com.example.doamin`:Book类

```java
package com.example.domain;

public class Book {
    private Integer id;
    private String name;
    private String type;

    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", type='" + type + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }
}
```

`com.example.dao`:BookDao接口

```java
package com.example.dao;

import com.example.domain.Book;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface BookDao {
    @Select("select * from book where id = #{id}")
    public Book getById(Integer id);
}
```

进行测试：

`test/java/com.example/Boot2MybatisApplicationTests`:

```java
package com.example;

import com.example.dao.BookDao;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Boot2MybatisApplicationTests {

    @Autowired
    private BookDao bookDao;

    @Test
    void contextLoads() {
        System.out.println(bookDao.getById(1));
    }

}
```

# 整合Mybatis-Plus

使用start.spring.io创建项目，没有提供依赖项MyBatis Plus Framework。（start.aliyun.com有提供）

可自行上网(https://mvnrepository.com/artifact/org.apache.maven)搜索MyBatis Plus的Maven配置写法。

使用：（其他写法与Mybatis相同，修改BookDao即可）

`com.example.dao`:BookDao接口

```java
package com.example.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.example.domain.Book;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface BookDao extends BaseMapper<Book> { 
    //通过泛型传入实体类，比如Book对应数据库中的表book（名称不对应运行报错）
}
```

配置表名称前缀：

`application.yml`添加：

```yml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: tb- # 此时对应表名称tb-book
```

测试：

```java
package com.example;

import com.example.dao.BookDao;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Boot3MybatisPlusApplicationTests {
    @Autowired
    private BookDao bookDao;

    @Test
    void contextLoads() {
        System.out.println(bookDao.selectById(2));
    }

}
```

# 整合Druid

创建项目（使用mybatis和mysql）

mvn网站搜索Druid（选择Druid Spring Boot Starter）

自行选择一个版本，复制其maven配置（添加到dependencies下）

在`application.yml`配置：

```yml
spring:
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/books
      username: root
      password: "123456"
```

# idea快捷键

Alt + 7 ：显示类结构

Ctrl + h ：显示类层次结构

Alt + Ins ：快速生成方法

Alt + Enter : 自动生成对象实例接收返回值

# demo

创建一个demo项目，使用用spring web、mybatis plus、mysql driver。

使用druid数据源，`pom.xml`加入：

```xml
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid-spring-boot-starter</artifactId>
  <version>1.2.9</version>
</dependency>
```

`application.yml`：

```yml
spring:
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/books
      username: root
      password: "123456"

server:
  port: 80

mybatis-plus:
  global-config:
    db-config:
      id-type: auto # 向数据库添加数据时id自动递增
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 开启mybatis-plus运行日志
```

创建表（book）结构如下：

| id   | name       | type   | description                                                  |
| ---- | ---------- | ------ | ------------------------------------------------------------ |
| 1    | springboot | 计算机 | *Spring Boot* 是 Pivotal 团队在 Spring 的基础上提供的一套全新的开源框架。 |

## 使用Lombok简化实体类开发

是一个java类库，提供了一组注解，简化POJO实体类开发

`pom.xml`加入：

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
</dependency>
```

使用：

```java
package com.example.domain;

import lombok.Data;

//自动生成getter、setter、toString、hashCode等方法
@Data
public class Book {
    private Integer id;
    private String name;
    private String type;
    private String description;
}
```

## 数据层开发

`com.example.Dao/BookDao`：

```java
package com.example.Dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.example.domain.Book;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface BookDao extends BaseMapper<Book> {
}
```

## 分页查询

定义配置类：`com.example.config/MPConfig`:

```java
package com.example.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

//定义为配置类
@Configuration
public class MPConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){ //mybatisPlus的拦截器
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor()); //提供分页功能的拦截器
        return interceptor;
    }

}
```

测试：

```java
@Test
void getPage() {
    IPage<Book> page = new Page<>(1, 5);
    bookDao.selectPage(page, null);
    System.out.println(page.getTotal()); //数据总数
    System.out.println(page.getCurrent()); //当前页
    System.out.println(page.getPages()); //总页数
    System.out.println(page.getSize()); //每页条数
    System.out.println(page.getRecords()); //查询结果
}
```

## 条件查询

```java
//两种写法
@Test
void getBy() {
    QueryWrapper<Book> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("name", "安全"); //查询name属性中含有“安全”的结果
    bookDao.selectList(queryWrapper);
}

@Test
void getBy2() {
    LambdaQueryWrapper<Book> queryWrapper = new LambdaQueryWrapper<>();
    queryWrapper.like(Book::getName, "安全"); //同上
    bookDao.selectList(queryWrapper);
}

@Test
void getBy3() {
    String name = null;
    LambdaQueryWrapper<Book> queryWrapper = new LambdaQueryWrapper<>();
    //在前面多传一个boolean参数（如果为false则不进行条件查询，而是输出表中所有结果）
    queryWrapper.like(name != null,Book::getName, name); 
    bookDao.selectList(queryWrapper);
}
```

## 业务层开发

`com.example.service/BookService`：

```java
package com.example.service;

import com.example.domain.Book;

import java.util.List;

public interface BookService {
    Boolean save(Book book);
    Boolean update(Book book);
    Boolean delete(Book book);
    Book getById(Integer id);
    List<Book> getAll();
}
```

`com.example.service.impl/BookServiceImpl`:

```java
package com.example.service.impl;

import com.example.Dao.BookDao;
import com.example.domain.Book;
import com.example.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookDao bookDao;

    @Override
    public Boolean save(Book book) {
        return bookDao.insert(book) > 0;
    }

    @Override
    public Boolean update(Book book) {
        return bookDao.updateById(book) > 0;
    }

    @Override
    public Boolean delete(Book book) {
        return bookDao.deleteById(book) > 0;
    }

    @Override
    public Book getById(Integer id) {
        return bookDao.selectById(id);
    }

    @Override
    public List<Book> getAll() {
        return bookDao.selectList(null);
    }
}
```

测试：

```java
@Autowired
private BookService bookService;

@Test
void getById(){
    System.out.println(bookService.getById(1));
}
```

### 快速开发（基于Mybatis-Plus）

自动生成基本的业务代码（增删改查）

`com.example.service/IBookService`：

```java
package com.example.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.example.domain.Book;

public interface IBookService extends IService<Book> {
}
```

`com.example.service.impl/IBookServiceImpl`:

```java
package com.example.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.example.Dao.BookDao;
import com.example.domain.Book;
import com.example.service.IBookService;
import org.springframework.stereotype.Service;

@Service
public class IBookServiceImpl extends ServiceImpl<BookDao, Book> implements IBookService {
}
```

测试：

```java
@Autowired
private IBookService iBookService;

@Test
void getAll() {
    System.out.println(iBookService.list());
}
```

## 表现层开发

`com.example.controller/BookController`:

```java
package com.example.controller;

import com.example.domain.Book;
import com.example.service.IBookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("books")
public class BookController {
    @Autowired
    private IBookService iBookService;

    @GetMapping
    public List<Book> getAll() {
        return iBookService.list();
    }

    @GetMapping("{id}")
    public Book getById(@PathVariable Integer id) {
        return niBookService.getById(id);
    }
    
    @PostMapping
    public Boolean save(@RequestBody Book book) {
        return iBookService.save(book);
    }

    @PutMapping
    public Boolean update(@RequestBody Book book) {
        return iBookService.updateById(book);
    }

    @DeleteMapping("{id}")
    public Boolean delete(@PathVariable Integer id) {
        Book book = new Book();
        book.setId(id);
        return iBookService.removeById(book);
    }

}

```

使用接口测试工具进行测试

## 消息一致性处理

统一前后端交互的数据格式。

返回结果封装成一个R对象

`com.example.controller.utils/R`:

```java
package com.example.controller.utils;

import lombok.Data;

@Data
public class R {
    private Boolean flag;
    private Object data;

    public R() {
    }

    public R(Boolean flag) {
        this.flag = flag;
    }

    public R(Boolean flag, Object data) {
        this.flag = flag;
        this.data = data;
    }
}
```

`com.example.controller/BookController`:

```java
package com.example.controller;

import com.example.controller.utils.R;
import com.example.domain.Book;
import com.example.service.IBookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("books")
public class BookController {
    @Autowired
    private IBookService iBookService;

    @GetMapping
    public R getAll() {
        return new R(true, iBookService.list());
    }

    @GetMapping("{id}")
    public R getById(@PathVariable Integer id) {
        return new R(true, iBookService.getById(id));
    }

    @PostMapping
    public R save(@RequestBody Book book) {
        return new R(iBookService.save(book));
    }

    @PutMapping
    public R update(@RequestBody Book book) {
        return new R(iBookService.updateById(book));
    }

    @DeleteMapping("{id}")
    public R delete(@PathVariable Integer id) {
        return new R(iBookService.removeById(id));
    }
}
```











[参考视频](https://www.bilibili.com/video/BV15b4y1a7yG?p=1&t=2.1)
