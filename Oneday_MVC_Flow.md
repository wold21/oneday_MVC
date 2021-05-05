# OneDay_MVC



## Project setting

1. <**org.springframework-version**>5.2.5.RELEASE</**org.springframework-version**>

   - 버전 변경.

2. Project Clean

3. Maven Clean

4. Maven Build -> Goals : compile -> RUN

5. Connect Tomcat with Project

6. Root address -> /

7. Timeout -> start 120s

8. Web.xml
   `src/main/webpp/WEB-INF/web.xml`

   ```xml
   <filter>
       <filter-name>encodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter
       </filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>UTF-8</param-value>
       </init-param>
       <init-param>
           <param-name>forceEncoding</param-name>
           <param-value>true</param-value>
       </init-param>
   </filter>
   <filter-mapping>
       <filter-name>encodingFilter</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
   ```

9. log4j.xml

   `src/main/resources/log4j.xml`

   ```xml
   <!-- Root Logger -->
   <root>
       <priority value="debug" />
       <appender-ref ref="console" />
   </root>
   ```

10. Delete HomeContoller and view 



## Creating a Book input screen 

1. create a book controller 

   ```java
   package sample.spring.oneday;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   }
   ```

2. create a book view page

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
       pageEncoding="UTF-8"%>
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="UTF-8">
   <title>책 생성하기</title>
   </head>
   	<body>
   		<h1>책 생성하기</h1>
   		<form method="POST">
   			<p>제목: <input type="text" name="title" /></p>
   			<p>카테고리: <input type="text" name="category" /></p>
   			<p>가격: <input type="text" name="price" /></p>
   			<p>제목: <input type="submit" name="Save" />
   		</form>
   	</body>
   </html>
   ```

3. Check Screen



## Database

1. create DB

2. create user and access auth -> 난 그냥 root로 사용함,

   ```mysql
   GRANT EXECUTE, SELECT, SHOW VIEW, ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, INDEX, INSERT, REFERENCES, TRIGGER, UPDATE, LOCK TABLES  ON `oneday_spring_mvc`.* TO 'oneday_spring_user'@'%' WITH GRANT OPTION;
   ```

   ```mysql
   FLUSH PRIVILEGES;
   ```

3. Create table

   ```mysql
   CREATE TABLE `book` (
    `book_id` BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    `title` VARCHAR(200) NOT NULL,
    `category` VARCHAR(200) NOT NULL DEFAULT '',
    `price` INT NULL,
    `insert_date` DATETIME NOT NULL DEFAULT NOW(),
    PRIMARY KEY (`book_id`)
   )
   COLLATE='utf8mb4_general_ci';
   ```

4. setting DB Library

   ~~~xml
   <!-- database -->
   <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.4</version>
   </dependency>
   
   <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis-spring</artifactId>
       <version>2.0.4</version>
   </dependency>
   
   <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>${org.springframework-version}</version>
   </dependency>
   
   <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-dbcp2 -->
   <dependency>
       <groupId>org.apache.commons</groupId>
       <artifactId>commons-dbcp2</artifactId>
       <version>2.7.0</version>
   </dependency>
   
   <!-- https://mvnrepository.com/artifact/org.bgee.log4jdbc-log4j2/log4jdbc-log4j2-jdbc4 -->
   <dependency>
       <groupId>org.bgee.log4jdbc-log4j2</groupId>
       <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
       <version>1.16</version>
   </dependency>
   
   <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.19</version>
   </dependency>
   ~~~

5. Maven Build

6. setting Data source

   데이터베이스에 접속하기 위한 정보를 설정한다. 외부 연동 관련 정보는 `context` 에서 설정한다.

   `src/main/webapp/WEB-INF/spring/root-context.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">
   
    <!-- Root Context: defines shared resources visible to all other web components -->
   
    <!-- for mysql -->
    <bean id="dataSource"
     class="org.apache.commons.dbcp2.BasicDataSource"
     destroy-method="close">
     <property name="driverClassName"
      value="com.mysql.cj.jdbc.Driver" />
     <property name="url"
      value="jdbc:mysql://localhost:3306/oneday_spring_mvc?serverTimezone=UTC&amp;useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
     <property name="username" value="root" />
     <property name="password" value="@Gur178432" />
    </bean>
   
    <bean id="sqlSessionFactory"
     class="org.mybatis.spring.SqlSessionFactoryBean">
     <property name="dataSource" ref="dataSource" />
     <property name="mapperLocations"
      value="classpath:/sqlmap/**/*_SQL.xml" />
    </bean>
    <bean id="sqlSessionTemplate"
     class="org.mybatis.spring.SqlSessionTemplate">
     <constructor-arg index="0" ref="sqlSessionFactory" />
    </bean>  
   </beans>
   ```



## Create a book input function

1. Create query xml -> book_SQL.xml

   `src/main/resources/sqlmap`

2. Write query

   `src/main/resources/sqlmap/book_SQL.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   </mapper>
   ```

3. Create Dao Class

   `src/main/java/sample/spring/yse/BookDao.java`

   ```java
   package sample.spring.oneday;
   
   import org.mybatis.spring.SqlSessionTemplate;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public class BookDao {
    @Autowired
    SqlSessionTemplate sqlSessionTemplate;
   }
   ```

4. Create service Class

   `src/main/java/sample/spring/yse/BookServiceImpl.java`

   ```java
   package sample.spring.oneday;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   }
   ```

5. Create Interface BookService

6. Create service class method

   `src/main/java/sample/spring/yse/BookServiceImpl.java`

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   	
   	@Override
   	public String create(Map<String, Object> map) {
   	    int affectRowCount = this.bookDao.insert(map);
   	    if (affectRowCount ==  1) {
   	        return map.get("book_id").toString();
   	    }
   	    return null;
   
   	}
   }
   ```

7. Create interface BookService method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   public interface BookService {
   
   	String create(Map<String, Object> map);
   }
   ```

8. Add service bean & method

   `src/main/java/sample/spring/yse/BookController.java`

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   
   	    return mav;
   	}
   }
   ```





## Book Detail

1. book detail search query

   `src/main/resources/sqlmap/book_SQL.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   	
   	<select id="select_detail" parameterType="hashMap" resultType="hashMap">
       <![CDATA[
           select
           title,
           category,
           price,
   	 insert_date
   	        from
   	        book
   	        where
   	        book_id = #{bookId}   
   	    ]]>
   	</select>
   </mapper>
   ```

2. Write Detail Dao

   `src/main/java/sample/spring/yse/BookDao.java`

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.mybatis.spring.SqlSessionTemplate;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public class BookDao {
   	@Autowired
   	SqlSessionTemplate sqlSessionTemplate;
   	 
   	 public int insert(Map<String, Object> map) {
   		  return this.sqlSessionTemplate.insert("book.insert", map);
   	 }
   	 
   	 public Map<String, Object> selectDetail(Map<String, Object> map) {
   		    return this.sqlSessionTemplate.selectOne("book.select_detail", map);
   	 }
   }
   ```

   `sqlSessionTemplate` 의 `selectOne` 메소드는 데이터를 한 개만 가져올 때 사용한다. 만약 쿼리 결과 행 수가 0개면 `selectOne` 메소드는 null을 반환하게 되며 쿼리 결과가 여러 개면 `TooManyResultsException` 예외를 던진다.

3. Create service class method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   	
   	@Override
   	public String create(Map<String, Object> map) {
   	    int affectRowCount = this.bookDao.insert(map);
   	    if (affectRowCount ==  1) {
   	        return map.get("book_id").toString();
   	    }
   	    return null;
   	}
   	
   	@Override
   	public Map<String, Object> detail(Map<String, Object> map){
   	    return this.bookDao.selectDetail(map);
   	}
   }
   ```

4. Create interface BookService method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   public interface BookService {
   
   	String create(Map<String, Object> map);
   
   	Map<String, Object> detail(Map<String, Object> map);
   }
   ```

5. Create Detail Controller method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
     
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   }
   ```

6. Create view detail.jsp

   ```jsp
   <%@ page pageEncoding="UTF-8" contentType="text/html;charset=utf-8"%>
   <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
   <html>
    <head>
     <title>책 상세</title>
    </head>
    <body>
     <h1>책 상세</h1>
     <p>제목 : ${ data.title } </p>
     <p>카테고리 : ${ data.category }</p>
     <p>가격 : <fmt:formatNumber type="number" maxFractionDigits="3" value="${data.price}" /></p> 
     <p>입력일 : <fmt:formatDate value="${data.insert_date}" pattern="yyyy.MM.dd HH:mm:ss" /></p>
   
     <p>
      <a href="/update?bookId=${bookId}">수정</a>
     </p>
     <form method="POST" action="/delete">
      <input type="hidden" name="bookId" value="${bookId}" />
      <input type="submit" value="삭제" />
     </form>
     <p>
      <a href="/list">목록으로</a>
     </p>
    </body>
   </html>
   ```

7. Check Screen





## Update Screen

1. Added Update method in Controller

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/update", method = RequestMethod.GET)  
   	public ModelAndView update(@RequestParam Map<String, Object> map) {  
   	Map<String, Object> detailMap = this.bookService.detail(map);  
   
   	ModelAndView mav = new ModelAndView();  
   	mav.addObject("data", detailMap);  
   	mav.setViewName("/book/update");  
   	return mav;  
   	}  
   }
   ```

2. Copy create.jsp -> rename -> update.jsp

   ```jsp
   <%@ page pageEncoding="UTF-8" contentType="text/html;charset=utf-8"%>  
   <html>  
   <head>  
   <title>책 수정</title>  
   </head>  
   	<body>  
   		<h1>책 수정</h1>  
   		<form method="POST">  
   			<p>제목 : <input type="text" name="title" value="${ data.title }" /></p>  
   			<p>카테고리 : <input type="text" name="category"  value="${ data.category }" /></p>  
   			<p>가격 : <input type="text" name="price"  value="${ data.price }" /></p>  
   			<p><input type="submit" value="저장" />  
   		</form>  
   	</body>  
   </html>
   ```

3. Check Screen





## Create Book Modify function

1. Added Query in book_SQL.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   	
   	<select id="select_detail" parameterType="hashMap" resultType="hashMap">
       <![CDATA[
           select
           title,
           category,
           price,
   	 insert_date
   	        from
   	        book
   	        where
   	        book_id = #{bookId}   
   	    ]]>
   	</select>
   	
   	<update id="update" parameterType="hashMap">  
   	<![CDATA[  
   		update book set  
   		title = #{title},  
   		category = #{category},  
   		price = #{price}  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</update>
   </mapper>
   ```

2. Create Modify fn Dao method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.mybatis.spring.SqlSessionTemplate;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public class BookDao {
   	@Autowired
   	SqlSessionTemplate sqlSessionTemplate;
   	 
   	 public int insert(Map<String, Object> map) {
   		  return this.sqlSessionTemplate.insert("book.insert", map);
   	 }
   	 
   	 public Map<String, Object> selectDetail(Map<String, Object> map) {
   		    return this.sqlSessionTemplate.selectOne("book.select_detail", map);
   	 }
   	 
   	 public int update(Map<String, Object> map) {  
   		 return this.sqlSessionTemplate.update("book.update", map);  
   	}  
   }
   ```

3. Create method in service class

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   	
   	@Override
   	public String create(Map<String, Object> map) {
   	    int affectRowCount = this.bookDao.insert(map);
   	    if (affectRowCount ==  1) {
   	        return map.get("book_id").toString();
   	    }
   	    return null;
   	}
   	
   	@Override
   	public Map<String, Object> detail(Map<String, Object> map){
   	    return this.bookDao.selectDetail(map);
   	}
   	
   	@Override
   	public boolean edit(Map<String, Object> map) {  
   	int affectRowCount = this.bookDao.update(map);  
   	return affectRowCount == 1;  
   	}  
   }
   ```

4. Create method in service interface

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   public interface BookService {
   
   	String create(Map<String, Object> map);
   
   	Map<String, Object> detail(Map<String, Object> map);
   
   	boolean edit(Map<String, Object> map);
     
   }
   ```

5. Added Controller Method

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/update", method = RequestMethod.GET)  
   	public ModelAndView update(@RequestParam Map<String, Object> map) {  
   		Map<String, Object> detailMap = this.bookService.detail(map);  
   	
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", detailMap);  
   		mav.setViewName("/book/update");  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "update", method = RequestMethod.POST)  
   	public ModelAndView updatePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isUpdateSuccess = this.bookService.edit(map);  
   		if (isUpdateSuccess) {  
   		String bookId = map.get("bookId").toString();  
   		mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}else {  
   		mav = this.update(map);  
   		}  
   		return mav;  
   	}  
   }
   ```





## Create delete function 

1. delete query

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   	
   	<select id="select_detail" parameterType="hashMap" resultType="hashMap">
       <![CDATA[
           select
           title,
           category,
           price,
   	 insert_date
   	        from
   	        book
   	        where
   	        book_id = #{bookId}   
   	    ]]>
   	</select>
   	
   	<update id="update" parameterType="hashMap">  
   	<![CDATA[  
   		update book set  
   		title = #{title},  
   		category = #{category},  
   		price = #{price}  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</update>
   	
   	<delete id="delete" parameterType="hashMap">  
   	<![CDATA[  
   		delete from book  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</delete>  
   </mapper>
   ```

2. create delete method in Dao

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.mybatis.spring.SqlSessionTemplate;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public class BookDao {
   	@Autowired
   	SqlSessionTemplate sqlSessionTemplate;
   	 
   	public int insert(Map<String, Object> map) {
   		  return this.sqlSessionTemplate.insert("book.insert", map);
   	 }
   	 
   	public Map<String, Object> selectDetail(Map<String, Object> map) {
   		    return this.sqlSessionTemplate.selectOne("book.select_detail", map);
   	 }
   	 
   	public int update(Map<String, Object> map) {  
   		 return this.sqlSessionTemplate.update("book.update", map);  
   	}
   	
   	public int delete(Map<String, Object> map) {  
   		return this.sqlSessionTemplate.delete("book.delete", map);  
   	}  	 
   }
   ```

3. create delete method in service class

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   	
   	@Override
   	public String create(Map<String, Object> map) {
   	    int affectRowCount = this.bookDao.insert(map);
   	    if (affectRowCount ==  1) {
   	        return map.get("book_id").toString();
   	    }
   	    return null;
   	}
   	
   	@Override
   	public Map<String, Object> detail(Map<String, Object> map){
   	    return this.bookDao.selectDetail(map);
   	}
   	
   	@Override
   	public boolean edit(Map<String, Object> map) {  
   		int affectRowCount = this.bookDao.update(map);  
   		return affectRowCount == 1;  
   	}  
   	
   	@Override
   	public boolean remove(Map<String, Object> map) {  
   		int affectRowCount = this.bookDao.delete(map);  
   		return affectRowCount == 1;  
   
   	} 
   }
   ```

4. create delete method in interface 

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   public interface BookService {
   
   	String create(Map<String, Object> map);
   
   	Map<String, Object> detail(Map<String, Object> map);
   
   	boolean edit(Map<String, Object> map);
   
   	boolean remove(Map<String, Object> map);
   
   }
   
   ```

5. create delete function in controller

   ```java
   package sample.spring.oneday;
   
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/update", method = RequestMethod.GET)  
   	public ModelAndView update(@RequestParam Map<String, Object> map) {  
   		Map<String, Object> detailMap = this.bookService.detail(map);  
   	
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", detailMap);  
   		mav.setViewName("/book/update");  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "update", method = RequestMethod.POST)  
   	public ModelAndView updatePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isUpdateSuccess = this.bookService.edit(map);  
   		if (isUpdateSuccess) {  
   			String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}else {  
   			mav = this.update(map);  
   		}  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "/delete", method = RequestMethod.POST)  
   	public ModelAndView deletePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isDeleteSuccess = this.bookService.remove(map);  
   		if (isDeleteSuccess) {  
   			mav.setViewName("redirect:/list");  
   		}else {  
   		String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}  
   		return mav;  
   	}  
   
   }
   ```

6. Check Screen





## Create Book list

1. write query

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   	
   	<select id="select_detail" parameterType="hashMap" resultType="hashMap">
       <![CDATA[
           select
           title,
           category,
           price,
   	 insert_date
   	        from
   	        book
   	        where
   	        book_id = #{bookId}   
   	    ]]>
   	</select>
   	
   	<update id="update" parameterType="hashMap">  
   	<![CDATA[  
   		update book set  
   		title = #{title},  
   		category = #{category},  
   		price = #{price}  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</update>
   	
   	<delete id="delete" parameterType="hashMap">  
   	<![CDATA[  
   		delete from book  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</delete>  
   	
   	<select id="select_list" parameterType="hashMap" resultType="hashMap">  
   	<![CDATA[  
   		select  
   		book_id,  
   		title,  
   		category,  
   		price,  
   		insert_date  
   		from  
   		book  
   		order by insert_date desc  
   		]]>  
   	</select>
   </mapper>
   ```

2. create book list Dao method

   ```java
   package sample.spring.oneday;
   
   import java.util.List;
   import java.util.Map;
   
   import org.mybatis.spring.SqlSessionTemplate;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Repository;
   
   @Repository
   public class BookDao {
   	@Autowired
   	SqlSessionTemplate sqlSessionTemplate;
   	 
   	public int insert(Map<String, Object> map) {
   		  return this.sqlSessionTemplate.insert("book.insert", map);
   	 }
   	 
   	public Map<String, Object> selectDetail(Map<String, Object> map) {
   		    return this.sqlSessionTemplate.selectOne("book.select_detail", map);
   	 }
   	 
   	public int update(Map<String, Object> map) {  
   		 return this.sqlSessionTemplate.update("book.update", map);  
   	}
   	
   	public int delete(Map<String, Object> map) {  
   		return this.sqlSessionTemplate.delete("book.delete", map);  
   	}  
   	
   	public List<Map<String, Object>> selectList(Map<String, Object> map) {  
   		return this.sqlSessionTemplate.selectList("book.select_list", map);  
   	}  
   	 
   }
   ```

3. create book list service class method

   ```java
   package sample.spring.oneday;
   
   import java.util.List;
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Service;
   
   @Service
   public class BookServiceImpl implements BookService {
   	@Autowired
   	BookDao bookDao;
   	
   	@Override
   	public String create(Map<String, Object> map) {
   	    int affectRowCount = this.bookDao.insert(map);
   	    if (affectRowCount ==  1) {
   	        return map.get("book_id").toString();
   	    }
   	    return null;
   	}
   	
   	@Override
   	public Map<String, Object> detail(Map<String, Object> map){
   	    return this.bookDao.selectDetail(map);
   	}
   	
   	@Override
   	public boolean edit(Map<String, Object> map) {  
   		int affectRowCount = this.bookDao.update(map);  
   		return affectRowCount == 1;  
   	}  
   	
   	@Override
   	public boolean remove(Map<String, Object> map) {  
   		int affectRowCount = this.bookDao.delete(map);  
   		return affectRowCount == 1;  
   	} 
   	
   	@Override
   	public List<Map<String, Object>> list(Map<String, Object> map){  
   		return this.bookDao.selectList(map);  
   	}  
   }
   ```

4. create book list service method in interface 

   ```java
   package sample.spring.oneday;
   
   import java.util.List;
   import java.util.Map;
   
   public interface BookService {
   
   	String create(Map<String, Object> map);
   
   	Map<String, Object> detail(Map<String, Object> map);
   
   	boolean edit(Map<String, Object> map);
   
   	boolean remove(Map<String, Object> map);
   
   	List<Map<String, Object>> list(Map<String, Object> map);
   
   }
   ```

5. create book list method in controller

   ```java
   package sample.spring.oneday;
   
   import java.util.List;
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/update", method = RequestMethod.GET)  
   	public ModelAndView update(@RequestParam Map<String, Object> map) {  
   		Map<String, Object> detailMap = this.bookService.detail(map);  
   	
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", detailMap);  
   		mav.setViewName("/book/update");  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "update", method = RequestMethod.POST)  
   	public ModelAndView updatePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isUpdateSuccess = this.bookService.edit(map);  
   		if (isUpdateSuccess) {  
   			String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}else {  
   			mav = this.update(map);  
   		}  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "/delete", method = RequestMethod.POST)  
   	public ModelAndView deletePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isDeleteSuccess = this.bookService.remove(map);  
   		if (isDeleteSuccess) {  
   			mav.setViewName("redirect:/list");  
   		}else {  
   		String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "list")  
   	public ModelAndView list(@RequestParam Map<String, Object> map) {  
   
   		List<Map<String, Object>> list = this.bookService.list(map);  
   
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", list);  
   		mav.setViewName("/book/list");  
   		return mav;  
   	}  
   
   }
   ```

6. create list.jsp

   ```jsp
   <%@ page pageEncoding="UTF-8" contentType="text/html;charset=utf-8"%>  
   <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>  
   <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>  
   <html>  
   	<head>  
   		<title>책 목록</title>  
   	</head>  
   	<body>  
   		<h1>책 목록</h1>  
   		<table>  
   			<thead>  
   				<tr>  
   					<td>제목</td>  
   					<td>카테고리</td>  
   					<td>가격</td>  
   				</tr>  
   			</thead>  
   			<tbody>  
   			<c:forEach var="row" items="${data}">  
   			<tr>  
   				<td>  
   				<a href="/detail?bookId=${row.book_id}">  
   				${row.title}  
   				</a>  
   				</td>  
   				<td>${row.category}</td>  
   				<td><fmt:formatNumber type="number" maxFractionDigits="3" value="${row.price}" /></td>  
   			</tr>  
   			</c:forEach>  
   			</tbody>  
   		</table>  
   		<p>  
   		<a href="/create">생성</a>  
   		</p>  
   	</body>  
   </html>  
   ```

7. Check Screen





## Added search function

1. write Search query

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE  mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="book">
   	<insert id="insert" parameterType="hashMap" useGeneratedKeys="true" keyProperty="book_id">  
   	    <![CDATA[
   	    insert into book
   	    (title, category, price) 
   	    values
   	    (#{title}, #{category}, #{price})
   	    ]]>
   	</insert>
   	
   	<select id="select_detail" parameterType="hashMap" resultType="hashMap">
       <![CDATA[
           select
           title,
           category,
           price,
   	 insert_date
   	        from
   	        book
   	        where
   	        book_id = #{bookId}   
   	    ]]>
   	</select>
   	
   	<update id="update" parameterType="hashMap">  
   	<![CDATA[  
   		update book set  
   		title = #{title},  
   		category = #{category},  
   		price = #{price}  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</update>
   	
   	<delete id="delete" parameterType="hashMap">  
   	<![CDATA[  
   		delete from book  
   		where  
   		book_id = #{bookId}  
   		]]>  
   	</delete>  
   	
   	<select id="select_list" parameterType="hashMap" resultType="hashMap">  
   	<![CDATA[  
   		select  
   		book_id,  
   		title,  
   		category,  
   		price,  
   		insert_date  
   		from  
   		book  
   		where 1 = 1  
   				]]> 
   		<if test="keyword != null and keyword != ''">
   		and (title like CONCAT('%',#{keyword},'%') or category like CONCAT('%',#{keyword},'%'))  
   		</if>		
   		order by insert_date desc
     
   	</select> 
   
   </mapper>
   ```

2. create search function method in controller

   ```java
   package sample.spring.oneday;
   
   import java.util.List;
   import java.util.Map;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.servlet.ModelAndView;
   
   @Controller
   public class BookContoroller {
   	
   	@Autowired
   	BookService bookService;
   	
   	@RequestMapping(value="/create", method = RequestMethod.GET)
   	public ModelAndView create() {
   		return new ModelAndView("book/create");
   	}
   	
   	@RequestMapping(value = "/create", method = RequestMethod.POST)
   	public ModelAndView createPost(@RequestParam Map<String, Object> map) {
   	    ModelAndView mav = new ModelAndView();
   
   	    String bookId = this.bookService.create(map);
   	    if (bookId == null) {
   	        mav.setViewName("redirect:/create");
   	    }else {
   	        mav.setViewName("redirect:/detail?bookId=" + bookId); 
   	    }  
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/detail", method = RequestMethod.GET)
   	public ModelAndView detail(@RequestParam Map<String, Object> map) {
   	    Map<String, Object> detailMap = this.bookService.detail(map);
   
   	    ModelAndView mav = new ModelAndView();
   	    mav.addObject("data", detailMap);
   	    String bookId = map.get("bookId").toString();
   	    mav.addObject("bookId", bookId);
   	    mav.setViewName("/book/detail");
   	    return mav;
   	}
   	
   	@RequestMapping(value = "/update", method = RequestMethod.GET)  
   	public ModelAndView update(@RequestParam Map<String, Object> map) {  
   		Map<String, Object> detailMap = this.bookService.detail(map);  
   	
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", detailMap);  
   		mav.setViewName("/book/update");  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "update", method = RequestMethod.POST)  
   	public ModelAndView updatePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isUpdateSuccess = this.bookService.edit(map);  
   		if (isUpdateSuccess) {  
   			String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}else {  
   			mav = this.update(map);  
   		}  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "/delete", method = RequestMethod.POST)  
   	public ModelAndView deletePost(@RequestParam Map<String, Object> map) {  
   		ModelAndView mav = new ModelAndView();  
   	
   		boolean isDeleteSuccess = this.bookService.remove(map);  
   		if (isDeleteSuccess) {  
   			mav.setViewName("redirect:/list");  
   		}else {  
   		String bookId = map.get("bookId").toString();  
   			mav.setViewName("redirect:/detail?bookId=" + bookId);  
   		}  
   		return mav;  
   	}  
   	
   	@RequestMapping(value = "list")  
   	public ModelAndView list(@RequestParam Map<String, Object> map) {
   
   		List<Map<String, Object>> list = this.bookService.list(map);  
   
   		ModelAndView mav = new ModelAndView();  
   		mav.addObject("data", list);		
   		if (map.containsKey("keyword")) {  
   			mav.addObject("keyword", map.get("keyword"));  
   		}   
   		mav.setViewName("/book/list");  
   		return mav;   
   	}  
   
   }
   
   ```

3. modify list.jsp

   ```jsp
   <%@ page pageEncoding="UTF-8" contentType="text/html;charset=utf-8"%>  
   <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>  
   <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>  
   <html>  
   	<head>  
   		<title>책 목록</title>  
   	</head>  
   	<body>  
   		<h1>책 목록</h1> 
   		<p>  
   			<form>  
   				<input type="text" placeholder="검색" name="keyword" value="${keyword}" />  
   				<input type="submit" value="검색" />  
   			</form>  
   		</p>  
   		<table>  
   			<thead>  
   				<tr>  
   					<td>제목</td>  
   					<td>카테고리</td>  
   					<td>가격</td>  
   				</tr>  
   			</thead>  
   			<tbody>  
   			<c:forEach var="row" items="${data}">  
   			<tr>  
   				<td>  
   				<a href="/detail?bookId=${row.book_id}">  
   				${row.title}  
   				</a>  
   				</td>  
   				<td>${row.category}</td>  
   				<td><fmt:formatNumber type="number" maxFractionDigits="3" value="${row.price}" /></td>  
   			</tr>  
   			</c:forEach>  
   			</tbody>  
   		</table>  
   		<p>  
   		<a href="/create">생성</a>  
   		</p>  
   	</body>  
   </html>  
   ```

   



