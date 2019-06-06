### 1.JPA是什么
>JPA顾名思义就是Java Persistence API的意思，是JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中。

### 2.使用JPA的优势
>2.1标准化
JPA 是 JCP 组织发布的 Java EE 标准之一，因此任何声称符合 JPA 标准的框架都遵循同样的架构，提供相同的访问API，这保证了基于JPA开发的企业应用能够经过少量的修改就能够在不同的JPA框架下运行。
2.2容器级特性的支持
JPA框架中支持大数据集、事务、并发等容器级事务，这使得 JPA 超越了简单持久化框架的局限，在企业应用发挥更大的作用。
2.3简单方便
JPA的主要目标之一就是提供更加简单的编程模型：在JPA框架下创建实体和创建Java 类一样简单，没有任何的约束和限制，只需要使用 javax.persistence.Entity进行注释，JPA的框架和接口也都非常简单，没有太多特别的规则和设计模式的要求，开发者可以很容易的掌握。JPA基于非侵入式原则设计，因此可以很容易的和其它框架或者容器集成。
2.4查询能力
JPA的查询语言是面向对象而非面向数据库的，它以面向对象的自然语法构造查询语句，可以看成是Hibernate HQL的等价物。JPA定义了独特的JPQL（Java Persistence Query Language），JPQL是EJB QL的一种扩展，它是针对实体的一种查询语言，操作对象是实体，而不是关系数据库的表，而且能够支持批量更新和修改、JOIN、GROUP BY、HAVING 等通常只有 SQL 才能够提供的高级查询特性，甚至还能够支持子查询。
2.4高级特性
JPA 中能够支持面向对象的高级特性，如类之间的继承、多态和类之间的复杂关系，这样的支持能够让开发者最大限度的使用面向对象的模型设计企业应用，而不需要自行处理这些特性在关系数据库的持久化。

更多的JPA知识可以自己相关学习下.

### 3.配置Spring Boot 数据源和JPA配置
![Jpa配置](https://img-blog.csdn.net/20180604003210742?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L214amVzc2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

1. 配置Spring Boot的maven依赖
	```
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<scope>runtime</scope>
	</dependency>
	```

2. Spring Boot的JPA配置
	```
	spring.jpa.hibernate.ddl-auto=update
	spring.jpa.show-sql=true
	```

3. Spring Boot数据库连接配置
	```
	spring.datasource.url=jdbc:mysql://localhost:3306/springboottest?useUnicode=true&characterEncoding=utf-8
	spring.datasource.username=root
	spring.datasource.password=123456
	spring.datasource.driver-class-name=com.mysql.jdbc.Driver
	```
 
### 4.创建测试实体类和测试方法
 
1. 创建实体类User类(图1位置)
	```
	@Entity
	@Table(name = "User")
	public class User {
	
		public User() {
			
		}
		
		public User(String username, int age) {
			this.username = username;
			this.age = age;
		}
		
		@Id
		@GeneratedValue(strategy = GenerationType.IDENTITY)
		private Integer id;
		
		@Column
		private String username;
		
		@Column
		private int age;
	
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
	
		public int getAge() {
			return age;
		}
	
		public void setAge(int age) {
			this.age = age;
		}
	}
	```

2.  定义接口，继承JpaRepository(图2位置)
	```
	public interface TestUserDao extends JpaRepository<User, Integer> {}
	```
3. 写入增删改查代码于测试类中(图3所示)
```
	@RunWith(SpringRunner.class)
	@SpringBootTest
	public class SpringBootDemo8ApplicationTests {
	
		@Test
		public void contextLoads() {
		}
	
		@Autowired
		private TestUserDao testUserDao;
		
		@Test
		public void insert() {
			User user= new User();
			user.setUsername("zhang san");
			user.setAge(23);
			testUserDao.save(user);
		}
	
		@Test
		public void update() {
			User user = new User();
			user.setId(1);
			user.setAge(18);
			user.setUsername("李四");
			testUserDao.save(user);
		}
		
		@Test
		public void select() {
			Optional<User> user = testUserDao.findById(1);
			System.out.println(user);
		}
		
		@Test
		public void delete() {
			testUserDao.deleteById(1);
		}
	}
	```
执行insert方法，可以看到console面板输出sql语句；查看数据库，能看到表已经被创建，同时插入了一条信息
	![insert输出sql](https://img-blog.csdn.net/20180604003819244?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L214amVzc2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
	![这里写图片描述](https://img-blog.csdn.net/20180604003941116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L214amVzc2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
依次执行以下几个测试方法，都能看到修改、查询和删除生效，此处配置已经都OK.

### 4. Spring JpaRepository其他查询方式

1. 除了以上基础的CRUD操作外，我们可以查询Spring Data JPA文档中找到很多使用方法，例如拼接两个条件的查询，我们可以在TestUserDao中创建接口方法`User findByUsernameAndAge(String string, int i);`，然后使用中调用此方法传入用户名和年龄查询.

2. 如果在API中提供的查询仍然无法满足我们的查询的话，我们可以在TestUserDao写入如下方法
	```
	@Query("select t from User t where t.username = ?1")
	User findByUsername(String username);
	```
	在测试类中，写入方法
	```
	@Test
	public void findByUsername() {
		User user = testUserDao.findByUsername("李四");
		System.out.println(user);
	}
	```
	执行结果
	![自定义jpa查询](https://img-blog.csdn.net/20180604011056586?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L214amVzc2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
此外，Spring JpaRepository还有其他很多方便的使用方法，有时间可以多了解下，这里就不多说.