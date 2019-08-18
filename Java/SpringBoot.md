# Spring Boot

## Spring Boot下的Spring MVC

SpringMvc：路由处理（视图跳转、请求处理）

| 注解            | 作用                                   |
| --------------- | -------------------------------------- |
| `@Controller`   | 处理请求                               |
| `@RestController` | @Controller与@ResponseBody的组合注解，用于返回字符串或json数据 |
| `@GetMapping`     | @RequestMapping与GET请求方法的结合 |
| `@PostMapping`  | @RequestMapping与POST请求方法的结合 |
| `@PutMapping`   | @RequestMapping与GET请求方法的结合 |
| `@DeleteMapping` | @RequestMapping与GET请求方法的结合 |

## Spring Boot下Mybatis配置

Mybatis：model层

1、配置依赖

修改`pom.xml`文件

2、配置Mapper.xml文件的位置

修改`application.properties`文件

```properties
mybatis.mapper-locations=classpath:com/ricez/mapper/*.xml
```

3、配置数据库

修改`application.properties`文件

```properties
spring.datasource.username=test
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/sp?useJDBCCompliantTimezoneShift=true&serverTimezone=UTC
```

4、在MyBatis的Mapper接口中添加@Mapper注解

或者在运行的主类上添加@Map派尔Scan("com.ricez.mapper")扫描注解

5、mpper.xml配置

示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.ricez.mapper.UserMapper">
    <resultMap id="userMap" type="com.ricez.model.User">
        <id column="id" jdbcType="INTEGER" property="id" />
        <result column="username" jdbcType="VARCHAR" property="username" />
        <result column="password" jdbcType="VARCHAR" property="password" />
    </resultMap>

    <sql id="column_list">
        id, username, password
    </sql>

    <select id="selectAllUser" resultMap="userMap">
        select
        <include refid="column_list"></include>
        from user
    </select>
</mapper>
```

