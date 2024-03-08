# SERVER RELATED DOCS


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
imagepathroot=C:\\cwdb\\img\\
#imagepathroot=/usr/btcrepo/img/   ## for linux
proofpathroot=C:\\cwdb\\proof\\
#proofpathroot=/usr/btcrepo/img/   ## for linux
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
├── training
│   ├── WEB-INF
│   │   ├──lib
|   |   |   ├──poi-ooxml-schemas-4.1.2.jar
```

