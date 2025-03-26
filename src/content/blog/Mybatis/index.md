---
title: "Springboot-Mybatis学习记录"
author: Zhidong Zhang
description: 记录学习 Javaweb 的 Mybatis 中的收获与遇到的一些问题。
publishDate: 2025-03-26
# updatedDate: 2025-03-02
tags:
  - Java
  - Javaweb
  - Mybatis
language: 中文
draft: false
# heroImage: {src: './cover.png' , color: '#9698C1'}
---

> 我听的课程为黑马23年的Javaweb课程，其中有一部分内容与现在(2025)有出入

## 遇到的问题

Springboot3中切换默认数据库连接池到Druid时需要在 `pom.xml` 中多配置一些内容，不然切换不成功：

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.21</version>
</dependency>
 
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-3-starter</artifactId>
   <version>1.2.21</version>
</dependency>
```

也就是在Springboot3中切换为Druid连接池必须添加 `druid-spring-boot-3-starter` 依赖，否则需要通过自定义配置类的方法才能切换成功。
配置类的代码：

```java
@Configuration
public class DataSourceConfig {
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.druid")
    public DataSource getDataSource() {
        return DruidDataSourceBuilder.create().build();
    }
}
```