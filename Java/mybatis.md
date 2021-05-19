# Mybatis

mybatis配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- mybatis的主配置文件 -->
<configuration>
    <!-- 配置环境 -->
    <environments default="mysql">
        <!-- 配置mysql的环境-->
        <environment id="mysql">
            <!-- 配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置数据源（连接池） -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的4个基本信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件 -->
    <mappers>
        <mapper resource="com/itheima/dao/IUserDao.xml"/>
    </mappers>
</configuration>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.dao.IUserDao">
    <!--配置查询所有-->
    <select id="findAll" resultType="com.itheima.domain.User">
        select * from user
    </select>
</mapper>
```

```
mybatis框架
共四天
第一天：mybatis入门
	mybatis的概述
	mybatis的环境搭建
	mybatis入门案例
	自定义mybatis框架（主要的目的是为了让大家了解mybatis中执行细节）
第二天：mybatis基本使用
	mybatis的单表crud操作
	mybatis的参数和返回值
	mybatis的dao编写
	mybatis配置的细节
		几个标签的使用
第三天：mybatis的深入和多表
	mybatis的连接池
	mybatis的事务控制及设计的方法
	mybatis的多表查询
		一对多（多对一）
		多对多
第四天：mybatis的缓存和注解开发
	mybatis中的加载时机（查询的时机）
	mybatis中的一级缓存和二级缓存
	mybatis的注解开发
		单表CRUD
		多表查询
-----------------------------------------------------------
1、什么是框架？
	它是我们软件开发中的一套解决方案，不同的框架解决的是不同的问题。
	使用框架的好处：
		框架封装了很多的细节，使开发者可以使用极简的方式实现功能。大大提高开发效率。
2、三层架构
	表现层：
		是用于展示数据的
	业务层：
		是处理业务需求
	持久层：
		是和数据库交互的
3、持久层技术解决方案
	JDBC技术：
		Connection
		PreparedStatement
		ResultSet
	Spring的JdbcTemplate：
		Spring中对jdbc的简单封装
	Apache的DBUtils：
		它和Spring的JdbcTemplate很像，也是对Jdbc的简单封装

	以上这些都不是框架
		JDBC是规范
		Spring的JdbcTemplate和Apache的DBUtils都只是工具类

4、mybatis的概述
	mybatis是一个持久层框架，用java编写的。
	它封装了jdbc操作的很多细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等繁杂过程
	它使用了ORM思想实现了结果集的封装。

	ORM：
		Object Relational Mappging 对象关系映射
		简单的说：
			就是把数据库表和实体类及实体类的属性对应起来
			让我们可以操作实体类就实现操作数据库表。

			user			User
			id			userId
			user_name		userName
	今天我们需要做到
		实体类中的属性和数据库表的字段名称保持一致。
			user			User
			id			id
			user_name		user_name
5、mybatis的入门
	mybatis的环境搭建
		第一步：创建maven工程并导入坐标
		第二步：创建实体类和dao的接口
		第三步：创建Mybatis的主配置文件
				SqlMapConifg.xml
		第四步：创建映射配置文件
				IUserDao.xml
	环境搭建的注意事项：
		第一个：创建IUserDao.xml 和 IUserDao.java时名称是为了和我们之前的知识保持一致。
			在Mybatis中它把持久层的操作接口名称和映射文件也叫做：Mapper
			所以：IUserDao 和 IUserMapper是一样的
		第二个：在idea中创建目录的时候，它和包是不一样的
			包在创建时：com.itheima.dao它是三级结构
			目录在创建时：com.itheima.dao是一级目录
		第三个：mybatis的映射配置文件位置必须和dao接口的包结构相同
		第四个：映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
		第五个：映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名

		当我们遵从了第三，四，五点之后，我们在开发中就无须再写dao的实现类。
	mybatis的入门案例
		第一步：读取配置文件
		第二步：创建SqlSessionFactory工厂
		第三步：创建SqlSession
		第四步：创建Dao接口的代理对象
		第五步：执行dao中的方法
		第六步：释放资源

		注意事项：
			不要忘记在映射配置中告知mybatis要封装到哪个实体类中
			配置的方式：指定实体类的全限定类名
		
		mybatis基于注解的入门案例：
			把IUserDao.xml移除，在dao接口的方法上使用@Select注解，并且指定SQL语句
			同时需要在SqlMapConfig.xml中的mapper配置时，使用class属性指定dao接口的全限定类名。
	明确：
		我们在实际开发中，都是越简便越好，所以都是采用不写dao实现类的方式。
		不管使用XML还是注解配置。
		但是Mybatis它是支持写dao实现类的。

6、自定义Mybatis的分析：
	mybatis在使用代理dao的方式实现增删改查时做什么事呢？
		只有两件事：
			第一：创建代理对象
			第二：在代理对象中调用selectList
		
	自定义mybatis能通过入门案例看到类
		class Resources
		class SqlSessionFactoryBuilder
		interface SqlSessionFactory
		interface SqlSession
```

基本流程

```java
package com.itheima.test;

import com.itheima.dao.IUserDao;
import com.itheima.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 * mybatis的入门案例
 */
public class MybatisTest {

    /**
     * 入门案例
     * @param args
     */
    public static void main(String[] args)throws Exception {
        //1.读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        //3.使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User> users = userDao.findAll();
        for(User user : users){
            System.out.println(user);
        }
        //6.释放资源
        session.close();
        in.close();
    }
}

```

配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
 PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!--
		1、mybatis可以使用properties来引入外部properties配置文件的内容；
		resource：引入类路径下的资源
		url：引入网络路径或者磁盘路径下的资源
	  -->
	<properties resource="dbconfig.properties"></properties>
	
	
	<!-- 
		2、settings包含很多重要的设置项
		setting:用来设置每一个设置项
			name：设置项名
			value：设置项取值
	 -->
	<settings>
		<setting name="mapUnderscoreToCamelCase" value="true"/>
	</settings>
	
	
	<!-- 3、typeAliases：别名处理器：可以为我们的java类型起别名 
			别名不区分大小写
	-->
	<typeAliases>
		<!-- 1、typeAlias:为某个java类型起别名
				type:指定要起别名的类型全类名;默认别名就是类名小写；employee
				alias:指定新的别名
		 -->
		<!-- <typeAlias type="com.atguigu.mybatis.bean.Employee" alias="emp"/> -->
		
		<!-- 2、package:为某个包下的所有类批量起别名 
				name：指定包名（为当前包以及下面所有的后代包的每一个类都起一个默认别名（类名小写），）
		-->
		<package name="com.atguigu.mybatis.bean"/>
		
		<!-- 3、批量起别名的情况下，使用@Alias注解为某个类型指定新的别名 -->
	</typeAliases>
		
	<!-- 
		4、environments：环境们，mybatis可以配置多种环境 ,default指定使用某种环境。可以达到快速切换环境。
			environment：配置一个具体的环境信息；必须有两个标签；id代表当前环境的唯一标识
				transactionManager：事务管理器；
					type：事务管理器的类型;JDBC(JdbcTransactionFactory)|MANAGED(ManagedTransactionFactory)
						自定义事务管理器：实现TransactionFactory接口.type指定为全类名
				
				dataSource：数据源;
					type:数据源类型;UNPOOLED(UnpooledDataSourceFactory)
								|POOLED(PooledDataSourceFactory)
								|JNDI(JndiDataSourceFactory)
					自定义数据源：实现DataSourceFactory接口，type是全类名
		 -->
		 

		 
	<environments default="dev_mysql">
		<environment id="dev_mysql">
			<transactionManager type="JDBC"></transactionManager>
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	
		<environment id="dev_oracle">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${orcl.driver}" />
				<property name="url" value="${orcl.url}" />
				<property name="username" value="${orcl.username}" />
				<property name="password" value="${orcl.password}" />
			</dataSource>
		</environment>
	</environments>
	
	
	<!-- 5、databaseIdProvider：支持多数据库厂商的；
		 type="DB_VENDOR"：VendorDatabaseIdProvider
		 	作用就是得到数据库厂商的标识(驱动getDatabaseProductName())，mybatis就能根据数据库厂商标识来执行不同的sql;
		 	MySQL，Oracle，SQL Server,xxxx
	  -->
	<databaseIdProvider type="DB_VENDOR">
		<!-- 为不同的数据库厂商起别名 -->
		<property name="MySQL" value="mysql"/>
		<property name="Oracle" value="oracle"/>
		<property name="SQL Server" value="sqlserver"/>
	</databaseIdProvider>
	
	
	<!-- 将我们写好的sql映射文件（EmployeeMapper.xml）一定要注册到全局配置文件（mybatis-config.xml）中 -->
	<!-- 6、mappers：将sql映射注册到全局配置中 -->
	<mappers>
		<!-- 
			mapper:注册一个sql映射 
				注册配置文件
				resource：引用类路径下的sql映射文件
					mybatis/mapper/EmployeeMapper.xml
				url：引用网路路径或者磁盘路径下的sql映射文件
					file:///var/mappers/AuthorMapper.xml
					
				注册接口
				class：引用（注册）接口，
					1、有sql映射文件，映射文件名必须和接口同名，并且放在与接口同一目录下；
					2、没有sql映射文件，所有的sql都是利用注解写在接口上;
					推荐：
						比较重要的，复杂的Dao接口我们来写sql映射文件
						不重要，简单的Dao接口为了开发快速可以使用注解；
		-->
		<!-- <mapper resource="mybatis/mapper/EmployeeMapper.xml"/> -->
		<!-- <mapper class="com.atguigu.mybatis.dao.EmployeeMapperAnnotation"/> -->
		
		<!-- 批量注册： -->
		<package name="com.atguigu.mybatis.dao"/>
	</mappers>
</configuration>
```

映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.atguigu.mybatis.dao.EmployeeMapper">
<!-- 
namespace:名称空间;指定为接口的全类名
id：唯一标识
resultType：返回值类型
#{id}：从传递过来的参数中取出id值
public Employee getEmpById(Integer id);
 -->
 
 	<!--public Map<Integer, Employee> getEmpByLastNameLikeReturnMap(String lastName);  -->
 	<select id="getEmpByLastNameLikeReturnMap" resultType="com.atguigu.mybatis.bean.Employee">
 		select * from tbl_employee where last_name like #{lastName}
 	</select>
 
 	<!--public Map<String, Object> getEmpByIdReturnMap(Integer id);  -->
 	<select id="getEmpByIdReturnMap" resultType="map">
 		select * from tbl_employee where id=#{id}
 	</select>
 
	<!-- public List<Employee> getEmpsByLastNameLike(String lastName); -->
	<!--resultType：如果返回的是一个集合，要写集合中元素的类型  -->
	<select id="getEmpsByLastNameLike" resultType="com.atguigu.mybatis.bean.Employee">
		select * from tbl_employee where last_name like #{lastName}
	</select>

 	<!-- public Employee getEmpByMap(Map<String, Object> map); -->
 	<select id="getEmpByMap" resultType="com.atguigu.mybatis.bean.Employee">
 		select * from ${tableName} where id=${id} and last_name=#{lastName}
 	</select>
 
 	<!--  public Employee getEmpByIdAndLastName(Integer id,String lastName);-->
 	<select id="getEmpByIdAndLastName" resultType="com.atguigu.mybatis.bean.Employee">
 		select * from tbl_employee where id = #{id} and last_name=#{lastName}
 	</select>
 	
 	<select id="getEmpById" resultType="com.atguigu.mybatis.bean.Employee">
		select * from tbl_employee where id = #{id}
	</select>
	<select id="getEmpById" resultType="com.atguigu.mybatis.bean.Employee"
		databaseId="mysql">
		select * from tbl_employee where id = #{id}
	</select>
	<select id="getEmpById" resultType="com.atguigu.mybatis.bean.Employee"
		databaseId="oracle">
		select EMPLOYEE_ID id,LAST_NAME	lastName,EMAIL email 
		from employees where EMPLOYEE_ID=#{id}
	</select>
	
	<!-- public void addEmp(Employee employee); -->
	<!-- parameterType：参数类型，可以省略， 
	获取自增主键的值：
		mysql支持自增主键，自增主键值的获取，mybatis也是利用statement.getGenreatedKeys()；
		useGeneratedKeys="true"；使用自增主键获取主键值策略
		keyProperty；指定对应的主键属性，也就是mybatis获取到主键值以后，将这个值封装给javaBean的哪个属性
	-->
	<insert id="addEmp" parameterType="com.atguigu.mybatis.bean.Employee"
		useGeneratedKeys="true" keyProperty="id" databaseId="mysql">
		insert into tbl_employee(last_name,email,gender) 
		values(#{lastName},#{email},#{gender})
	</insert>
	
	<!-- 
	获取非自增主键的值：
		Oracle不支持自增；Oracle使用序列来模拟自增；
		每次插入的数据的主键是从序列中拿到的值；如何获取到这个值；
	 -->
	<insert id="addEmp" databaseId="oracle">
		<!-- 
		keyProperty:查出的主键值封装给javaBean的哪个属性
		order="BEFORE":当前sql在插入sql之前运行
			   AFTER：当前sql在插入sql之后运行
		resultType:查出的数据的返回值类型
		
		BEFORE运行顺序：
			先运行selectKey查询id的sql；查出id值封装给javaBean的id属性
			在运行插入的sql；就可以取出id属性对应的值
		AFTER运行顺序：
			先运行插入的sql（从序列中取出新值作为id）；
			再运行selectKey查询id的sql；
		 -->
		<selectKey keyProperty="id" order="BEFORE" resultType="Integer">
			<!-- 编写查询主键的sql语句 -->
			<!-- BEFORE-->
			select EMPLOYEES_SEQ.nextval from dual 
			<!-- AFTER：
			 select EMPLOYEES_SEQ.currval from dual -->
		</selectKey>
		
		<!-- 插入时的主键是从序列中拿到的 -->
		<!-- BEFORE:-->
		insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
		values(#{id},#{lastName},#{email<!-- ,jdbcType=NULL -->}) 
		<!-- AFTER：
		insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
		values(employees_seq.nextval,#{lastName},#{email}) -->
	</insert>
	
	<!-- public void updateEmp(Employee employee);  -->
	<update id="updateEmp">
		update tbl_employee 
		set last_name=#{lastName},email=#{email},gender=#{gender}
		where id=#{id}
	</update>
	
	<!-- public void deleteEmpById(Integer id); -->
	<delete id="deleteEmpById">
		delete from tbl_employee where id=#{id}
	</delete>
	
	
</mapper>
```

