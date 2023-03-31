# SpringMVC Hibernate Java Configuration


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

