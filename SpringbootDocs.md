# SPRINGBOOT DOCS


## 1. Spring Mysql Configuration

The following configurations required for Springboot Mysql Config inside **application.properties**

```properties
server.port = 'server port'
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://db_ip/db_name
spring.datasource.username=username
spring.datasource.password=password
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
spring.jpa.show-sql=true
```


## 2. Folder structure declarations

If file operations are involved, use this configurations in **application.properties** file in springboot

```properties
var_name1=C:\\folder1\\       ## for windows
#var_name2=/dir1/dir2/dir3/   ## for linux
var_name3=C:\\folder1\\       ## for windows
#var_name4=/dir1/dir3/dir4/   ## for linux
```


## 3. Limiting File input size on overall SpringBoot Application

If file size limitations are required use this configuration in **application.properties** file

```properties
spring.servlet.multipart.max-file-size=2MB
spring.servlet.multipart.max-request-size=2MB
```


## 4. Excel Exporter Service for ooxml library

If app crash occurs while generating excel reports while using ooxml library

Remove the JAR **poi-ooxml-schemas-4.1.2.jar** from the following directory after deployment

```bash
├── project (ex: if war deployed is project.war)
│   ├── WEB-INF
│   │   ├──lib
|   |   |   ├──poi-ooxml-schemas-4.1.2.jar
```

