# Mybatis

简单的一个mybatis项目





#### 导入maven依赖

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
```

#### mybatis的核心配置文件

包括数据源datasource和事物管理,以及注册定义的Mapper.xml

这里useSSL可能要设置为false

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="*****"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/mybatis01/dao/userMapper.xml"/>
    </mappers>
</configuration>
```

##### 一,定义实体类

```java
package com.mybatis01.pojo;

public class user {
    private Integer id;
    private String  name;

    public user() {
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

    @Override
    public String toString() {
        return "user{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }

    public void setName(String name) {
        this.name = name;
    }

    public user(Integer id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

##### 二,定义mapper接口+mapper配置文件/注解(sql模板)

```java
public interface userMapper {
    public List<user> getUserList();
}
```

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis01.dao.userMapper">
    <select id="getUserList" resultType="com.mybatis01.pojo.user">
        select * from user
  </select>
</mapper>
```

namespace="***" 是用于绑定userMapper),定义的sql语句配置其实就是用于实现接口.

##### 三,mybatis工具类用于产生sqlSession对象

```java
public class mybatisUtils {
    public static SqlSessionFactory sqlSessionFactory;
    static {
        String resource = "mybatis.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    }
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }

}

```



##### 四, 执行SQL(获得实现Mapper接口的对象,内部使用反射机制实现,从而调用相应方法)



查询测试

```java
    @Test
    public void getUserList(){

        SqlSession sqlSession = mybatisUtils.getSqlSession();

        userMapper mapper = sqlSession.getMapper(userMapper.class);

        List<user> userList = mapper.getUserList();

        for(user user:userList){
            System.out.println(user);
        }
    }
```

插入测试

```java
public interface userMapper {
    public List<user> getUserList();
    public void addUser(user user);
}
```

```xml
<insert id="addUser" parameterType="com.mybatis01.pojo.user">
    insert into mybatis.user(id,name)values (#{id},#{name})
</insert>
```

```java
@Test
public void addUserTest(){
    SqlSession sqlSession = mybatisUtils.getSqlSession();
    userMapper mapper = sqlSession.getMapper(userMapper.class);
    mapper.addUser(new user(5,"5"));
    sqlSession.commit();
    sqlSession.close();

}
```

> 注意增删改是需要commit的





涉及maven的东西

![image-20210110163749664](https://github.com/kalao/Images/blob/master/spring基础.md/20210110163749664.png)
