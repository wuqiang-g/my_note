# 1. 动态sql

![image-20230530142358365](https://cdn.jsdelivr.net/gh/wuqiang-g/picture@main/img/image-20230530142358365.png)

## 1.1 if语句

根据 username 和 sex 来查询数据。如果username为空，那么将只根据sex来查询；反之只根据username来查询

```xml
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user where
        <if test="username != null">
           username=#{username}
        </if>
         
        <if test="username != null">
           and sex=#{sex}
        </if>
</select>
```



这样写我们可以看到，如果 sex 等于 null，那么查询语句为 select * from user where username=#{username},但是如果usename 为空呢？那么查询语句为 select * from user where and sex=#{sex}，这是错误的 SQL 语句，如何解决呢？请看下面的 where 语句



## 1.2 if+where 语句

```xml
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user
    <where>
        <if test="username != null">
           username=#{username}
        </if>
         
        <if test="username != null">
           and sex=#{sex}
        </if>
    </where>
</select>
```

 这个“where”标签会知道如果它包含的标签中有返回值的话，它就插入一个‘where’。此外，如果标签返回的内容是以and 或or 开头的，则它会剔除掉。



## 1.3 if+set 语句

 同理，上面的对于查询 SQL 语句包含 where 关键字，如果在进行更新操作的时候，含有 set 关键词，我们怎么处理呢？

```xml
<!-- 根据 id 更新 user 表的数据 -->
<update id="updateUserById" parameterType="com.ys.po.User">
    update user u
        <set>
            <if test="username != null and username != ''">
                u.username = #{username},
            </if>
            <if test="sex != null and sex != ''">
                u.sex = #{sex}
            </if>
        </set>
     
     where id=#{id}
</update>
```

这种写法，可以自动去除最后一个，



## 1.4 choose(when,otherwise) 语句

 有时候，我们不想用到所有的查询条件，只想选择其中的一个，查询条件有一个满足即可，使用 choose 标签可以解决此类问题，类似于 Java 的 switch 语句。

```xml
<select id="selectUserByChoose" resultType="com.ys.po.User" parameterType="com.ys.po.User">
      select * from user
      <where>
          <choose>
              <when test="id !='' and id != null">
                  id=#{id}
              </when>
              <when test="username !='' and username != null">
                  and username=#{username}
              </when>
              <otherwise>
                  and sex=#{sex}
              </otherwise>
          </choose>
      </where>
  </select>
```

也就是说，这里我们有三个条件，id,username,sex，只能选择一个作为查询条件

   如果 id 不为空，那么查询语句为：select * from user where  id=?

   如果 id 为空，那么看username 是否为空，如果不为空，那么语句为 select * from user where  username=?;

   如果 username 为空，那么查询语句为 select * from user where sex=?



## 1.5 trim 语句

trim标记是一个格式化的标记，可以完成set或者是where标记的功能



  ①、用 trim 改写上面第二点的 if+where 语句

```xml
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
        select * from user
        <!-- <where>
            <if test="username != null">
               username=#{username}
            </if>
             
            <if test="username != null">
               and sex=#{sex}
            </if>
        </where>  -->
        <trim prefix="where" prefixOverrides="and | or">
            <if test="username != null">
               and username=#{username}
            </if>
            <if test="sex != null">
               and sex=#{sex}
            </if>
        </trim>
    </select>
```

 prefix：前缀    

  prefixoverride：去掉第一个and或者是or



  ②、用 trim 改写上面第三点的 if+set 语句

```xml
<!-- 根据 id 更新 user 表的数据 -->
    <update id="updateUserById" parameterType="com.ys.po.User">
        update user u
            <!-- <set>
                <if test="username != null and username != ''">
                    u.username = #{username},
                </if>
                <if test="sex != null and sex != ''">
                    u.sex = #{sex}
                </if>
            </set> -->
            <trim prefix="set" suffixOverrides=",">
                <if test="username != null and username != ''">
                    u.username = #{username},
                </if>
                <if test="sex != null and sex != ''">
                    u.sex = #{sex},
                </if>
            </trim>
         
         where id=#{id}
    </update>
```

 suffix：后缀  

  suffixoverride：去掉最后一个逗号（也可以是其他的标记，就像是上面前缀中的and一样



## 1.6 foreach 语句

 需求：我们需要查询 user 表中 id 分别为1,2,3的用户

  sql语句：select * from user where id=1 or id=2 or id=3

​        select * from user where id in (1,2,3)



①、我们用 foreach 来改写 select * from user where id=1 or id=2 or id=3

```xml
<select id="selectUserByListId" parameterType="com.ys.vo.UserVo" resultType="com.ys.po.User">
    select * from user
    <where>
        <!--
            collection:指定输入对象中的集合属性
            item:每次遍历生成的对象
            open:开始遍历时的拼接字符串
            close:结束时拼接的字符串
            separator:遍历对象之间需要拼接的字符串
            select * from user where 1=1 and (id=1 or id=2 or id=3)
          -->
      	<if test="ids != null and ids.size > 0">
        	<foreach collection="ids" item="id" open="and (" separator="or" close=")">
            id=#{id}
        	</foreach>
        </if>
    </where>
</select>
```



  ②、我们用 foreach 来改写 select * from user where id in (1,2,3)

```xml
<select id="selectUserByListId" parameterType="com.ys.vo.UserVo" resultType="com.ys.po.User">
        select * from user
        <where>
            <!--
                collection:指定输入对象中的集合属性
                item:每次遍历生成的对象
                open:开始遍历时的拼接字符串
                close:结束时拼接的字符串
                separator:遍历对象之间需要拼接的字符串
                select * from user where 1=1 and id in (1,2,3)
              -->
          <if test="ids != null and ids.size > 0">
            <foreach collection="ids" item="id" open="and id in (" separator="," close=") " >
                #{id}
            </foreach>
           </if>
        </where>
    </select>
```

# 2. sql 复用

有时候可能某个 sql 语句我们用的特别多，为了增加代码的重用性，简化代码，我们需要将这些代码抽取出来，然后使用时直接调用。

  比如：假如我们需要经常根据用户名和性别来进行联合查询，那么我们就把这个代码抽取出来，如下：

```xml
<!-- 定义 sql 片段 -->
<sql id="selectUserByUserNameAndSexSQL">
    <if test="username != null and username != ''">
        AND username = #{username}
    </if>
    <if test="sex != null and sex != ''">
        AND sex = #{sex}
    </if>
</sql>
```

引用 sql 片段

```xml
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user
    <trim prefix="where" prefixOverrides="and | or">
        <!-- 引用 sql 片段，如果refid 指定的不在本文件中，那么需要在前面加上 namespace -->
        <include refid="selectUserByUserNameAndSexSQL"></include>
        <!-- 在这里还可以引用其他的 sql 片段 -->
    </trim>
</select>
```

注意：①、最好基于 单表来定义 sql 片段，提高片段的可重用性

​     ②、在 sql 片段中最好不要包括 where .

# 3. xml文件java接口类绑定

## 3.1 xml 文件与接口绑定

通过xml的 namespace 来绑定接口的全限定名。com.xxx.xxx.mapper.xxxDOMapper是mapper接口的全路径

```java
public interface xxxDOMapper{
}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xxx.xxx.mapper.xxxDOMapper" >
  <!--动态sql-->
</mapper>
```



## 3.2 xmlsql方法与接口方法名绑定，不支持方法的重载。

通过id属性来绑定，id属性值为方法名

```java
public interface xxxDOMapper{
	String getName(@Param("id")Long id);
}
<select id="getName" resultType="java.lang.String">
	select name form t_user where id=#{id};
</select>
```



## 3.3 xml方法的出入参数类型与java参数绑定

### 3.3.1 方法出参

1 ）一般通过xml方法里面的resultType和resultMap来指定方法返回值类型。

1.1）如果是**Java基本类型**,一般写全限定名

```xml
<select id="getName" resultType="java.lang.String">
	select name form t_user where id=#{id};
</select>
```

1.2）**javaBean类型**,这里一般是与表字段一一对应的DO或者entity类，如果字段名不对应(表字段dcc_code,类字段名dccCode)，建议用resultMap来指定字段映射

```xml
<select id="get" resultType="com.tx.springboottestdemo.entity.User">
    select * from user_test where id = #{id}
</select>
```

1.3）**List类型，这里返回的还是javaBean类型。**

```xml
mapper 接口：
List<User> getUsers();
  
SQL映射文件：
<select id="getUsers" resultType="com.tx.entity.User">
select * from user
</select>
```

2）如果数据库字段名与javabean字段名不匹配，就可以使用resultMap来做映射

```xml
  <resultMap id="BaseResultMap" type="com.alibaba.rhino.dcc.statistics.dal.entity.DeviceEventLogDO">
    <result column="dcc_id" jdbcType="BIGINT" property="dccId" />
    <result column="dcc_type" jdbcType="VARCHAR" property="dccType" />
    <result column="event_time" jdbcType="DATETIME" property="eventTime" />
  </resultMap>

<select id="get" resultMap="BaseResultMap"></select>
```

## 3.3.2 方法入参

 1）一般用parameterType来指定，但是一般不用它，而是在接口方法中用注解@Param指定,这样该可以区别多个有多个入参。



```java
Long count(@Param("query") DeviceEventLogBaseQuery query);
<select id="page" resultMap="BaseResultMap">
	select * from t_user where id = #{query.id}
</select>
```