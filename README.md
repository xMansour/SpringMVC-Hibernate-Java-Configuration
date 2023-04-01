# SpringMVC Hibernate Java Configuration

1. Setup the database and tables. For example i'm creating a user, product, and order relation. There is going to be a Many to Many relation between products and orders. And One to Many relation between the user and orders.

User Table
```
CREATE TABLE user (
id INT PRIMARY KEY,
first_name VARCHAR(40),
last_name VARCHAR(40),
email varchar(40),
birth_day DATE,
sex VARCHAR(1)
);
```

Product Table
```
CREATE TABLE product (
id INT PRIMARY KEY,
name VARCHAR(40),
price float
);
```

Orders Table

```
CREATE TABLE orders (
id INT PRIMARY KEY,
user_id int references user.id,
status boolean,
FOREIGN KEY (user_id) REFERENCES user(id)
);

```
Orders Products Table that is going to be used for the many to many mapping between orders and products.

```
CREATE TABLE orders_products (
id INT PRIMARY KEY,
order_id int,
product_id int,
quantity int,
FOREIGN KEY (order_id) REFERENCES orders(id),
FOREIGN KEY (product_id) REFERENCES product(id)
);
```



1. Add these SpringMVC dependencies under the dependencies tag inside the pom.xml file

```
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.3.23</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.23</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.23</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>5.3.23</version>
        </dependency>

```


2. Add these Hibernate dependencies under the dependencies tag inside the pom.xml file

```
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>5.6.11.Final</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.4.1.Final</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.32</version>
        </dependency>

```

3. Add the JSTL dependency under the dependencies tag inside the pom.xml file

```
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
```

4. Add the packaging under the version tag inside the pom.xml file
```
    <packaging>war</packaging>
```
5. Remove the directories under src and make directory as source root.
6. Add your packages under the src directory. (controllers, services, daos, entities, config).
7. Under src directory add a sub-directory called web it is going to hold the .jsp pages.
8. Edit configurations and add tomcat local server.
9. Fix the ```Warning: No artifacts marked for deployment``` by clicling fix and select war_exploded and change the application context to only ```/```
10. Change the Web resource directory from facets inside the project structure.
11. Inside the config package add the RootConfig class and add the @Configuration annotation over it.
```
@Configuration
public class RootConfig {

}
```
12. Add the WebConfig class and annotate it with @Configuration, @EnableWebMvc, and @ComponentScan(basePackages = "com.mansour.****"). Inside this class there should be a @Bean returned by the viewResolver method. This bean has its prefix = "/" and the suffix = ".jsp".
```
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.mansour.crm")
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```
13. Create the WebInit class that extends the AbstractAnnotationConfigDispatcherServletInitializer and overrides all its methods and the getRootConfigClasses should return the RootConfig.class, the getServletConfigClasses should return the WebConfig.class, and the getServletMappings should return the "/"

```
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{RootConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}

```


14. Create the HibernateConfig class and it should contain three beans: sessionFactory, dataSource, and getTransactionManager. It should be annotated with @Configuratuin, @EnableTransactionManagement, @ComponentScan(basePackages = "com.mansour.*****"), and @PropertySource("classpath:application.properties"). And a Environment instance should be injected inside it using @AutoWired. The application.properties file should be under src.

```

@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages = "com.mansour.crm")
@PropertySource("classpath:application.properties")
public class HibernateConfig {

    @Autowired
    private Environment environment;


    @Bean
    public LocalSessionFactoryBean sessionFactory() {
        LocalSessionFactoryBean sessionFactory = new LocalSessionFactoryBean();
        sessionFactory.setDataSource(dataSource());
        sessionFactory.setPackagesToScan("com.mansour.crm");
        sessionFactory.setHibernateProperties(hibernateProperties());
        return sessionFactory;
    }


    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(environment.getRequiredProperty("jdbc.driverClassName"));
        dataSource.setUrl(environment.getRequiredProperty("mysql.url"));
        dataSource.setUsername(environment.getRequiredProperty("mysql.username"));
        dataSource.setPassword(environment.getRequiredProperty("mysql.password"));
        return dataSource;
    }

    private Properties hibernateProperties() {
        Properties properties = new Properties();
        properties.put("hibernate.dialect", environment.getRequiredProperty("hibernate.dialect"));
        properties.put("hibernate.show_sql", environment.getRequiredProperty("hibernate.show_sql"));
        properties.put("hibernate.format_sql", environment.getRequiredProperty("hibernate.format_sql"));
        properties.put("hibernate.hbm2ddl.auto", environment.getRequiredProperty("hibernate.hbm2ddl.auto"));
        return properties;
    }


    @Bean
    public HibernateTransactionManager getTransactionManager() {
        HibernateTransactionManager transactionManager = new HibernateTransactionManager();
        transactionManager.setSessionFactory(sessionFactory().getObject());
        return transactionManager;
    }


}


```

15. The application.properties should be under src and should contain the hibernate config parameters.
```
jdbc.driverClassName = com.mysql.cj.jdbc.Driver
mysql.url = jdbc:mysql://localhost:3306/DATABASE_NAME
mysql.username = DATABASE_USER
mysql.password = DATABASE_PASSWORD
hibernate.dialect = org.hibernate.dialect.MySQLDialect
hibernate.show_sql = true
hibernate.format_sql = true
hibernate.hbm2ddl.auto = validate
```
. Create the home controller class
```
@Controller
public class HomeController {
    @GetMapping("/")
    public String index() {
        return "home";
    }
}


```

. Create home.jsp under the src/web folder
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Home</title>
</head>
<body>
<h1>Home Page</h1>
<a href="${pageContext.request.contextPath}/user/list">List Users</a>
<a href="${pageContext.request.contextPath}/product/list">List Products</a>
<a href="${pageContext.request.contextPath}/order/list">List Orders</a>
</body>
</html>
```
If you have nothing in the home page and want to be redirected use ```<% response.sendRedirect("customer/list");%>``` on the top of the file.



To avoid "from" unexpected in queries add Hibernate to the modules.

