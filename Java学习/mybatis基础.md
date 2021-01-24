







# Mybatis

## 简单的一个mybatis项目





### 一、导入maven依赖

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

### 二、mybatis的核心配置文件

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



### 三、mybatis使用的基本流程



#### 3.1、定义实体类

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

#### 3.2 、定义mapper接口+mapper配置文件/注解(sql模板)

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

#### 3.3、mybatis工具类用于产生sqlSession对象

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



#### 3.4、通过sqlsession获得mapper执行SQL(获得实现Mapper接口的对象,内部使用反射机制实现,从而调用相应方法)

##### 查询测试

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

##### 插入测试

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

注意:

1. 我们可以用map来代替实体类作为参数 

##### 模糊查询

```java
select * from user where name like "%李%"
1 传入 "%李%"
2 写死
    select * from user where name like "%"#{parm}"%"
```

### 四、配置

1. mybatis可以配置成适用多种环境(mysql,oracle)
2. mybatis的事物管理是JDBC,连接池是POOLED
3. 可以将数据库的配置用db.properties 来导入到mybatis中

MORE DETAILS:https://mybatis.org/mybatis-3/zh/configuration.html#environments

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!--   引入db.properties -->
    <properties resource="db.properties"></properties>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/mybatis01/dao/userMapper.xml"/>
    </mappers>
</configuration>
```

### 五、可能遇到的问题

1. 在java文件夹下的xml文件无法find,需要对java里的文件进行过滤,将其作为resources的一部分来源.

![image-20210110163749664](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210110163749664.png)

### 五、[类别别名](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)

1. 在mybatis配置文件下加入,定义com.mybatis01.pojo.user的别名为user

   ```xml
   <typeAliases >
       <typeAlias type="com.mybatis01.pojo.user" alias="user"></typeAlias>
   </typeAliases>
   ```

2. 在userMapper.xml下使用

```xml
<select id="getUserList" resultType="user">
      select * from user
</select>
```

1. 还可以指定一个包,那么mybatis会在这个包下去搜索java bean,默认是小写类名去对应这个类.

   ```xml
       <typeAliases >
           <package name="com.mybatis01.pojo"/>
       </typeAliases>
   ```

   

2. 在上面指定包的基础上,还可以使用注解的方式自定义包下某些类的别名

```
@Alias("hi")
public class user {
}
```

下面是一些为常见的 Java 类型内建的类型别名。它们都是不区分大小写的，注意，为了应对原始类型的命名重复，采取了特殊的命名风格。(节选一部分)

| 别名     | 映射的类型 |
| :------- | :--------- |
| _byte    | byte       |
| _long    | long       |
| _short   | short      |
| _int     | int        |
| _integer | int        |
| _double  | double     |
| _float   | float      |
| _boolean | boolean    |
| string   | String     |
| byte     | Byte       |
| long     | Long       |
| short    | Short      |
| int      | Integer    |
| integer  | Integer    |



### 六、设置

| 设置名             | 描述                                                         | 有效值        | 默认值 |
| :----------------- | :----------------------------------------------------------- | :------------ | :----- |
| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false | true   |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false | false  |



### 七、插件（plugins）

- mybatis-plus
- mybatis-generator



### 八、映射器（mappers）



![image-20210111104525952](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111104525952.png)

1. 对于上面第三种方式和第四种,可能会遇到一些问题
   - ​	主要有类和xml不在一个包内
   - ​    类和xml名称不一致

### 九 、生命周期和作用域

![image-20210111110539872](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111110539872.png)

![image-20210111111148142](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111111148142.png)

mapper对应一个业务(实现类)

### 十 、 resultMap

当数据库的column和实体类的property不匹配的时候使用

```
<resultMap id="usermap" type="user">
    <result column="id" property="id"></result>
    <result column="name" property="value"></result>
</resultMap>
```

对于一样的字段result可以省略

```
<resultMap id="usermap" type="user">
    <result column="name" property="value"></result>
</resultMap>
```

### 十一、 分页

```xml
<select id="getUserListLimit" parameterType="map" resultType="com.mybatis01.pojo.user">
    select * from user limit #{startIndex},#{pageSize}
</select>
```

### 十二、 注解

```java
@Select("select * from user")
public List<user> getUserList();
```

​	在核心配置中定义mapper接口,下面必须保证没有对应的xml文件,不然注解就失效了

```xml
    <mappers>
<!--        <mapper resource="com/mybatis01/dao/userMapper1.xml"/>-->
        <mapper class="com.mybatis01.dao.userMapper"></mapper>
    </mappers>
```



 对于含有多个参数(基本类型或者String类型)的必须在参数前面加@Parm("")



### 十三、 执行过程

![image-20210111221443343](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111221443343.png)

![image-20210111221531389](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111221531389.png)



![image-20210111221549476](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111221549476.png)



![image-20210111221648880](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111221648880.png)



### 十四、 关系处理

#### 1、 多对一

- comment评论

![image-20210112164533328](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112164533328.png)

- blog博客

![image-20210112164812845](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112164812845.png)



```java
@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class comment {
    private Integer id;
    private String content;
    private blog blog;
}
```

```java
@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class blog {
    private Integer id;
    private String title;
}
```



##### mybatis里面实现子查询

```xml
    <resultMap id="BolgComments" type="com.mybatis01.pojo.comment">
        <result property="id" column="id"></result>
        <association property="blog" column="bid"  javaType="com.mybatis01.pojo.blog" select="getBlogs">
        </association>
    </resultMap>
    <select id="getBlogs"  resultType="com.mybatis01.pojo.blog">
        select * from blog where id=#{id}
    </select>
    <select id="getComment" resultMap="BolgComments" >
        select * from comment
    </select>
```

```
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
```




- javaType:把sql语句查询出的结果集,封装给某个类的对象
- select:下一条要执行的sql语句
- column:在上一次查询结果集中,用哪些列值作为条件去执行下一条SQL语句.

##### mybatis的结果集嵌套处理

```java
<resultMap id="bmap" type="com.mybatis01.pojo.comment">
      <result property="id" column="cid"></result>
        <result property="content" column="content"></result>
        <association property="blog" javaType="com.mybatis01.pojo.blog">
            <result property="id" column="blogid"></result>
            <result property="title" column="title"></result>
        </association>
    </resultMap>
    <select id="getComments" resultMap="bmap">
       select blog.id blogid,comment.id cid,title,content  from blog,comment where blog.id=comment.bid
    </select>
```

```java
public interface blogMapper {
    List<comment> getComments();
}
```

```
[com.mybatis01.dao.blogMapper.getComments]-==>  Preparing: select blog.id blogid,comment.id cid,title,content from blog,comment where blog.id=comment.bid 
[com.mybatis01.dao.blogMapper.getComments]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getComments]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
```



#### 2 、 一对多

- comment评论


![image-20210112164533328](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112164533328.png)

- blog博客


![image-20210112164812845](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112164812845.png)

```java
@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class comment {
    private Integer id;
    private String content;
}
```

```java
@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class blog {
    private Integer id;
    private String title;
    private List<comment> comments;
}
```

##### mybatis结果集嵌套

```xml
<resultMap id="Bmap" type="com.mybatis01.pojo.blog">
    <result property="id" column="blogid"></result>
    <result property="title" column="title"></result>
    <collection property="comments" ofType="com.mybatis01.pojo.comment">
        <result property="id" column="cid"></result>
        <result property="content" column="content"></result>
    </collection>
</resultMap>
<select id="getBlog" resultMap="Bmap">
    select blog.id blogid,comment.id cid,title,content  from blog,comment where 	      blog.id=comment.bid and blog.id=#{id}
</select>
```


```java
public interface blogMapper {
    blog getBlog(@Param("id") Integer id);
}
```

```
输出结果:
[com.mybatis01.dao.blogMapper.getBlog]-==>  Preparing: select blog.id blogid,comment.id cid,title,content from blog,comment where blog.id=comment.bid and blog.id=? 
[com.mybatis01.dao.blogMapper.getBlog]-==> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlog]-<==      Total: 3
blog(id=1, title=starting, comments=[comment(id=1, content=so good), comment(id=2, content=agree you), comment(id=3, content=up)])

```

mybatis子查询方式:

和下图类似略

![image-20210112184947578](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112184947578.png)



### 十五、动态SQL

![image-20210112192114991](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112192114991.png)



#### IF

> 用于拼接一些条件,在前面没有条件的时候可以使用1=1 ,这样后面拼接才能用and **

```xml
<resultMap id="bmap" type="com.mybatis01.pojo.comment">
  <result property="id" column="cid"></result>
    <result property="content" column="content"></result>
    <association property="blog" javaType="com.mybatis01.pojo.blog">
        <result property="id" column="blogid"></result>
        <result property="title" column="title"></result>
    </association>
</resultMap>
<select id="getComments" resultMap="bmap">
        select blog.id blogid,comment.id cid,title,content  from blog,comment where blog.id=comment.bid
        <if test="title!=null">
            and title like "%"#{title}"%"
        </if>
        <if test="content!=null">
            and content like "%"#{content}"%"
        </if>

</select>
```

- 

```java
Map<String,Object> map=new HashMap<>();
map.put("title","sta");
map.put("content","g");
for(comment comment :mapper.getComments(map)){
    System.out.println(comment);
}
```

```
[com.mybatis01.dao.blogMapper.getComments]-==>  Preparing: select blog.id blogid,comment.id cid,title,content from blog,comment where blog.id=comment.bid and title like "%"?"%" and content like "%"?"%" 
[com.mybatis01.dao.blogMapper.getComments]-==> Parameters: sta(String), g(String)
[com.mybatis01.dao.blogMapper.getComments]-<==      Total: 2
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
```

- 

```java
Map<String,Object> map=new HashMap<>();
map.put("title","std");
for(comment comment :mapper.getComments(map)){
    System.out.println(comment);
}
```



```
[com.mybatis01.dao.blogMapper.getComments]-==>  Preparing: select blog.id blogid,comment.id cid,title,content from blog,comment where blog.id=comment.bid and title like "%"?"%" 
[com.mybatis01.dao.blogMapper.getComments]-==> Parameters: std(String)
[com.mybatis01.dao.blogMapper.getComments]-<==      Total: 0
```



#### where

> *where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。



官网:https://mybatis.org/mybatis-3/zh/dynamic-sql.html

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

#### choose、when、otherwise

> 有时候，我们不想使用所有的条件，而只是想从  **多个条件中选择一个**  使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

注意在外层最好嵌套一个where

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
   <where>
       <choose>
           <when test="title != null">
               AND title like #{title}
           </when>
           <when test="author != null and author.name != null">
               AND author_name like #{author.name}
           </when>
           <otherwise>
               AND featured = 1
           </otherwise>
       </choose>
    </where>
</select>
```

#### set 

> 这个例子中，*set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

#### sql片段

![image-20210112202328124](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112202328124.png)

#### foreach

in (xx,xx,xx)

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

in (xx or xx or xx)

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="or" close=")">
        #{item}
  </foreach>
</select>
```

### 十六、缓存

#### 缓存

![image-20210112203721141](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112203721141.png)

#### mybatis缓存

![image-20210112204702021](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112204702021.png)

![image-20210112210512950](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112210512950.png)

##### 一级缓存

![image-20210112214532212](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112214532212.png)

查看mybatis默认的缓存

```java
        SqlSession sqlSession = mybatisUtils.getSqlSession();
        blogMapper mapper = sqlSession.getMapper(blogMapper.class);
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        sqlSession.close();
```



```
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Created connection 192881625.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Returned connection 192881625 to pool.

```

##### 二级缓存

![image-20210112214513948](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112214513948.png)

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。 为了使它更加强大而且易于配置，我们对 MyBatis 3 中的缓存实现进行了许多改进。

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```
<cache/>
```

![image-20210112215210255](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112215210255.png)

基本上就是这样。这个简单语句的效果如下:

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。

- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。  (猜测每个sqlsession独享,有待验证)

这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。

可用的清除策略有：

- `LRU` – 最近最少使用：移除最长时间不被使用的对象。
- `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
- `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
- `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

默认的清除策略是 LRU。

flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。

size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。

readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。 因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。 速度上会慢一些，但是更安全，因此默认值是 false。



实验验证了 **映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存**

```java
        SqlSession sqlSession = mybatisUtils.getSqlSession();
        blogMapper mapper = sqlSession.getMapper(blogMapper.class);
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        mapper.updateComment(new comment(1,"-------",null));
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        sqlSession.close();
```



```
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Created connection 192881625.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
[com.mybatis01.dao.blogMapper.updateComment]-==>  Preparing: update comment set content=? where id=? 
[com.mybatis01.dao.blogMapper.updateComment]-==> Parameters: -------(String), 1(Integer)
[com.mybatis01.dao.blogMapper.updateComment]-<==    Updates: 1
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=-------, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Rolling back JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Returned connection 192881625 to pool.

```

```
        SqlSession sqlSession = mybatisUtils.getSqlSession();
        blogMapper mapper = sqlSession.getMapper(blogMapper.class);
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        sqlSession.close();
        sqlSession = mybatisUtils.getSqlSession();
        mapper = sqlSession.getMapper(blogMapper.class);
//        mapper.updateComment(new comment(1,"-------",null));
        for (comment comment : mapper.getComment()) {
            System.out.println(comment);
        }
        sqlSession.close();
```

未设置二级缓存

```
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Created connection 192881625.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Returned connection 192881625 to pool.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Checked out connection 192881625 from pool.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@b7f23d9]
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
```

设置二级缓存(注意对象要实现Serializable接口)

```
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Created connection 1931444790.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@731f8236]
[com.mybatis01.dao.blogMapper.getComment]-==>  Preparing: select * from comment 
[com.mybatis01.dao.blogMapper.getComment]-==> Parameters: 
[com.mybatis01.dao.blogMapper]-Cache Hit Ratio [com.mybatis01.dao.blogMapper]: 0.0
[com.mybatis01.dao.blogMapper.getBlogs]-====>  Preparing: select * from blog where id=? 
[com.mybatis01.dao.blogMapper.getBlogs]-====> Parameters: 1(Integer)
[com.mybatis01.dao.blogMapper.getBlogs]-<====      Total: 1
[com.mybatis01.dao.blogMapper.getComment]-<==      Total: 3
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@731f8236]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@731f8236]
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Returned connection 1931444790 to pool.
[com.mybatis01.dao.blogMapper]-Cache Hit Ratio [com.mybatis01.dao.blogMapper]: 0.3333333333333333
comment(id=1, content=so good, blog=blog(id=1, title=starting))
comment(id=2, content=agree you, blog=blog(id=1, title=starting))
comment(id=3, content=up, blog=blog(id=1, title=starting))
```



![image-20210112220220434](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112220220434.png)

![image-20210112223820984](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112223820984.png)



# 日志

## 一、log4j

什么是log4j?

- log4j是apache的一个开源项目,通过使用log4j,我们可以控制日志信息输送的目的地是控制台,文件,GUI组件
- 我们可以控制每一条日志的输出格式;
- 通过定义每一条日志信息的级别,我们能够更加细致地控制日志的生成过程.
- 通过一个配置文件来灵活地进行配置,而不需要修改应用的代码

导包

![image-20210111194737875](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111194737875.png)



<img src="https://github.com/kalao/Images/blob/master/mybatis基础.md/20210111193940703.png" alt="image-20210111193940703" style="zoom:80%;" />

```
log4j.rootLogger=DEBUG,CONSOLE,logfile

# 配置CONSOLE输出到控制台
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.Target=System.out
# 配置CONSOLE设置为自定义布局模式
log4j.appender.CONSOLE.Threshold=DEBUG
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout 
log4j.appender.CONSOLE.layout.ConversionPattern=[%c]-%m%n

################
# 输出到日志文件中
################

# 配置logfile输出到文件中 文件大小到达指定尺寸的时候产生新的日志文件
log4j.appender.logfile=org.apache.log4j.RollingFileAppender
# 保存编码格式
log4j.appender.logfile.Encoding=UTF-8
# 输出文件位置此为项目根目录下的logs文件夹中
log4j.appender.logfile.File=./log/root.log
# 后缀可以是KB,MB,GB达到该大小后创建新的日志文件
log4j.appender.logfile.MaxFileSize=10MB
# 设置滚定文件的最大值3 指可以产生root.log.1、root.log.2、root.log.3和root.log四个日志文件
log4j.appender.logfile.MaxBackupIndex=3  
# 配置logfile为自定义布局模式
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=[%p][%d{yyyy-MM-dd HH:mm:ss}][%c] %m%n

log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

# lombok插件

导入依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>
```

![image-20210112124311934](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210112124311934.png)



# 注解Annotation

> 是从JDK5.0开始引入的新技术

### 注解的作用:

1. 不是程序本身,可以对程序作出解释
2. 可以被其他程序读取

### 作用对象:

- 在package,class,method,field上面,相当于给他们添加了

  额外的辅助信息,我们可以通过反射机制实现对这些元数据的访问.

示例:

@Override 重写的注解

@Deprecated 不推荐使用

@SuppressWarnings("")抑制警告

### 元注解:

> 元注解的作用是负责注解其他注解

@Target :表示我们的注解可以放在哪些地方

@Retention 表示我们的注解在什么地方(时候)有效

```
@Retention(value=RetentionPolicy.RUNTIME)
```

runtime>class>sources

@Documented 表示是否将我们的注解生成在JAVAdoc中

@Inherited:说明子类可以继承父类中的该注解

### 自定义注解

> 使用@interface自定义注解时,自动继承了java.lang.annotation.Annotation接口

![image-20210113165509210](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113165509210.png)

在自定义注解的时候,注解的参数是类似于函数形式,名称就是参数的名称,参数的返回值只能是基本类型,class,String,enum,参数必须要有值,一般会给默认的值(default关键词来付给参数默认值)

```java
@Target(ElementType.METHOD)
@interface myano{
    String school() default "清华大学";
}

@myano(school="武汉理工大学")
```

# 反射

### 动态语言

> 可以根据某些条件改变自身结构,python,js,php都是动态语言
>
> 举个例子,比如在js中一串文本,js可以将其执行.就是类型可以变换,不仅仅是强制类型转换这种,很随意的.(个人理解)

### 静态语言

> 运行时候结构不变.java不是动态语言,但java可以通过反射机制获得类似动态语言的特性

> 反射是java被视为动态语言的关键,反射机制允许程序在执行期间借助reflection API取得任何类的内部信息,并能操作任意对象的内部属性以及方法.

```java
Class c=Class.forName("java.lang.String")
```

> 加载完类之后,在堆内存的方法区就产生了一个Class类型的对象(一个类只有一个Class对象),这个对象就包含了完整的类的结构信息.我们可以通过这个对象看到类的结构.这个对象就像一面镜子,通过这个镜子看到类的结构,所以我们形象称之为:反射.

![image-20210113191612842](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113191612842.png)

### Java反射机制提供的功能

![image-20210113192105149](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113192105149.png)

##### Java反射优点和缺点

![image-20210113192358954](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113192358954.png)



##### Java反射的API

![image-20210113192510784](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113192510784.png)

### Class类

```java
Class  c = Class.forName("com.mybatis01.pojo.user");
```

> 这里反射个人理解是从对象反向映射对应的类

![image-20210113194225186](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113194225186.png)

![image-20210113194635518](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113194635518.png)

#### class类常用方法

![image-20210113194745744](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113194745744.png)

#### 获取Class类的实例

![image-20210113194824761](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113194824761.png)

#### 哪些对象有Class对象

![image-20210113200030006](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113200030006.png)

#### 类的加载过程

![image-20210113203532124](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113203532124.png)

![image-20210113211151989](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113211151989.png)

![image-20210113213150611](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113213150611.png)

![image-20210113210822799](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113210822799.png)



![image-20210113211010625](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113211010625.png)

初始化是按照定义的顺序来

![image-20210113210431304](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113210431304.png)





![image-20210113212413315](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210113212413315.png)

 



class方法示例:

![image-20210114104719550](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114104719550.png)

![image-20210114104800994](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114104800994.png)

![image-20210114110614768](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114110614768.png)

![image-20210114111424705](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114111424705.png)

![image-20210114111746072](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114111746072.png)

#### 反射调用指定的方法

![image-20210114111826580](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114111826580.png)

![image-20210114111913420](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114111913420.png)

#### 反射获得泛型信息

![image-20210114112353123](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114112353123.png)

![image-20210114112718041](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114112718041.png)



#### 反射获得注解

![image-20210114131429549](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114131429549.png)

![image-20210114131704889](https://github.com/kalao/Images/blob/master/mybatis基础.md/20210114131704889.png)
