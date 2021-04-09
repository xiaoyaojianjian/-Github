# CH01_Hibernate框架的搭建

1.Hibernate.cfg.xml:Hibermate的配置文件，数据库的配置信息

2.创建持久化类

3.创建持久化类的映射文件

4.通过hibernateAPI操控数据，进行数据持久化

Hibernate.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC "-//Hibernate/Hibernate Configuration DTD 3.0//EN" "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>

 <session-factory>
  <!-- 数据库相关信息-->
  <!--方言，不同数据有不同的方言  -->
  <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
  <!-- 驱动 -->
  <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
  <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernate?useUnicode=true&amp;characterEncoding=UTF-8</property><!-- 设置字符编码集 -->
  <!-- 关于中文乱码
  1.创建eclipse项目编码方式，项目上右键、properties查看
  2.创建 数据库UTF-8
  3.创建表的字符集UTF-8
  4.表的中文字段字符集UTF-8
  5.hibernate连接数据库的url设置编码方式-->
  <property name="hibernate.connection.username">root</property>
  <property name="hibernate.connection.password"></property>
  <!--可选， 在控制台打印格式化sql语句 -->
  <property name="hibernate.show_sql">true</property>
  <property name="hibernate.format_sql">true</property>
  
  <!-- 设置映射文件 -->
  <mapping resource="com/hibernate/entity/Customer.hbm.xml"/>
  <mapping resource="com/hibernate/entity/User.hbm.xml"/>
</session-factory>
</hibernate-configuration>

```

User.java

```java
package com.hibernate.entity;

public class User {
	private Integer id;
	private String username;
	private String password;
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	@Override
	public String toString() {
		return "User [id=" + id + ", username=" + username + ", password=" + password + "]";
	}
	
}

```

User.hbm.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.hibernate.entity">
	<class name="User" table="user">
	<id name="id" column="id">
	<generator class="increment"></generator>
	</id>
	<!-- type:引用类型进行权限命名，基本类型直接写 
	column当字段名与属性名一致，可省略
	access:
	Property：默认值，表明Hibernate通过set和get方法访问实体类的属性，适用于没有属性但有get和set方法的实体类映射；
	Field：表明Hibernate通过Java反射机制直接访问类的属性。适用于没有get和set方法的实体类映射；-->
	<property name="username" column="username" type="java.lang.String" not-null="true"></property>
	<property name="password" column="password" access="property"></property>
	</class>
</hibernate-mapping>
```

hibernateUtil.java

```java
public class HibernateUtil {

	private static SessionFactory sessionFactory;
	
	/**
	 * 静态初始化Hibernate
	 */
	static {
		//1.创建ServiceRegistry对象,读取hibernate.cfg.xml在根目录下，则不用写参数
		StandardServiceRegistry registry = new StandardServiceRegistryBuilder()
				.configure().build();
		
		try {
			//2.创建SessionFactory对象，简单了解
			sessionFactory = new MetadataSources(registry).buildMetadata()
					.buildSessionFactory();
		}catch (Exception e) {
			e.printStackTrace();
			//手动释放StandardServiceRegistry对象
			StandardServiceRegistryBuilder.destroy(registry);
		}
	}
	
	/**
	 * 创建Session对象,session是与数据库之间的会话
	 */
	public static Session openSession() {
		return sessionFactory.openSession();
	}
	
	/**
	 * 关闭SessionFactory
	 */
	public static void closeSessionFactory() {
		sessionFactory.close();
	}
	
}
```

Test.java

```java
package com.hibernate.ui;

import org.hibernate.Session;
import org.hibernate.Transaction;

import com.hibernate.entity.User;
import com.hibernate.util.HibernateUtil;

public class UserTest {

	public static void main(String[] args) {
//		saveUser();
//		getUserById();
//		updateUser();
		deleteUser();
		HibernateUtil.closeSessionFactory();
	}
	public static void saveUser() {
		Session session=HibernateUtil.openSession();
		Transaction transaction=session.beginTransaction();
		try {
			User user=new User();
			user.setUsername("张三");
			user.setPassword("123");
			session.save(user);
			transaction.commit();
		} catch (Exception e) {
			transaction.rollback();
		}finally {
			session.close();
		}
	}
	
	public static void getUserById() {
		Session session = HibernateUtil.openSession();
		//get()通过主键查询
		User user=session.get(User.class, new Integer(1));
		System.out.println(user);
	}
	
	public static void updateUser() {
		Session session=HibernateUtil.openSession();
		Transaction transaction=session.beginTransaction();
		try {
			//查询得到需要更新的User
			User user=session.get(User.class, 1);
			user.setPassword("456");
			session.update(user);
			transaction.commit();
		} catch (Exception e) {
			transaction.rollback();
		}finally {
			session.close();
		}
	}
	
	public static void deleteUser() {
		Session session=HibernateUtil.openSession();
		Transaction transaction=session.beginTransaction();
		try {
			//查询得到需要删除的User
			User user=session.get(User.class, 1);
			//删除
			session.delete(user);
			transaction.commit();
		} catch (Exception e) {
			transaction.rollback();
		}finally {
			session.close();
		}
	}

}

```

# CH02_单实体映射









