如果你有使用 JDBC 或其他类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句有多么痛苦。拼接的时候要确保不能忘了必要的空格，还要注意省掉列名列表最后的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。

动态 SQL 元素和使用 JSTL 或其他类似基于 XML 的文本处理器相似

主要标签：
- `if`
- `trim (where, set)`
- `foreach`

## If

    使用场景1：
    查询用户，搜索条件可以是user_name,gender,email中的任意多个，使用if标签可以方便快速的解决，省去繁琐的sql拼接代码

    使用场景2：
    类似的在修改时，因为我们不知道具体修改哪几列，我们需要先查询出数据的全部数据，再把要修改的数据做替换。而利用If标签可以只对要修改的列进行修改，不需要先查询数据

`多条件搜索：`

```xml
 <select id="selectAllSearch" resultType="User">
  		select * from user where 1=1
  		<if test="user_name!=null">
  			and user_name like #{user_name}
  		</if>
  		<if test="gender!=null">
  			and gender like #{gender}
  		</if>
  		<if test="email!=null">
  			and email like #{email}
  		</if>
  </select>
```

## Where

    上面示例中，where后面跟了1=1,mybatis觉得这种处理方式不雅，加入where标签。可以智能识别 `and` 和`or`,把多出来的and或者or去掉

```xml
<select id="selectAllSearch" resultType="User">
    select * from user 
    <where>
      <if test="name!=null">
        and user_name like #{userName}
      </if>
      <if test="gender!=null">
        and gender like #{gender}
      </if>
      <if test="email!=null">
        and email like #{email}
      </if>
    </where>
</select>
```

## Set

去掉修改案例中可能多余的,
```xml
<update id="updateDynamic">
    update user
    <set>
      <if test="userName!=null">
            user_name=#{userName},
      </if> 	
      <if test="gender!=null">
            gender=#{gender},
      </if> 
      <if test="email!=null">
            email =#{email},
      </if> 		
    </set>
    where id=#{id}
</update>
```

## Trim

trim是where 和 set的通用版，不建议使用(where,set看起来更加直观)

```xml
  <select id="selectAllSearch" resultType="User">
  		select * from user 
  		<trim prefix="where" prefixOverrides="and|or">
  			<if test="name!=null">
	  			and user_name like #{name}
	  		</if>
	  		<if test="gender!=null">
	  			and gender like #{gender}
	  		</if>
	  		<if test="email!=null">
	  			and email like #{email}
	  		</if>
  		</trim>
  </select>
```

```xml
  <update id="updateDynamic">
  		<trim prefix="set" suffixOverrides=",">
  				<if test="userName!=null">
	  			 user_name=#{userName},
	  		</if> 	
	  		<if test="gender!=null">
	  			 gender=#{gender},
	  		</if> 
			<if test="email!=null">
	  			 email =#{email},
	  		</if> 	
  		</trim>
  		where id=#{id}
  </update>
```
## Foreach

动态 SQL 的另外一个常用的必要操作是需要对一个集合进行遍历，通常是在构建 IN 条件语句的时候。比如：

示例： 删除复选框选中的多行数据：

```xml
<delete id="deleteAll">
  delete from user 
  <where>
    id in 
    <foreach item="id" open="(" close=")" collection="ids" separator=",">
      #{id}
    </foreach>
</where>
</delete>
```

