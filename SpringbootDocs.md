# SPRINGBOOT DOCS

## 1. Spring Mysql Configuration

The following configurations required for Springboot Mysql Config inside **application.properties**

```properties
server.port = 'server port'
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://db_ip/db_name
spring.datasource.username=username
spring.datasource.password=password
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
spring.jpa.show-sql=true
```

## 2. Folder structure declarations

If file operations are involved, use this configurations in **application.properties** file in springboot

```properties
var_name1=C:\\folder1\\       ## for windows
var_name2=/dir1/dir2/dir3/    ## for linux
```

## 3. Limiting File input size on overall SpringBoot Application

If file size limitations are required use this configuration in **application.properties** file

```properties
spring.servlet.multipart.max-file-size=2MB
spring.servlet.multipart.max-request-size=2MB
```

## 4. Adding Custom Error Page in place of Default error page in Tomcat

Make a custom error page as a static html page and save it as customerror.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Error</title>
</head>
<body>
    <h1>Something Went Wrong</h1>
</body>
</html>
```

Place this customerror.html file inside /ROOT folder under /webapps folder

Then open web.xml file located inside /conf directory in Tomcat installation directory.
Place the following configuration in web.xml inside 'web-app' element

```xml
<web-app>
    ... other lines
    <error-page>
        <location>/customerror.html</location> <!-- specify the location of your custom error page -->
    </error-page>
</web-app>
```

Restart Tomcat after making the changes

## 5. Excel Exporter Service for ooxml library

If app crash occurs while generating excel reports while using ooxml library

Remove the JAR **poi-ooxml-schemas-4.1.2.jar** and **ooxml-schemas-1.1.jar** from the following directory after deployment

```tree
├── project (ex: if war deployed is project.war)S
│   ├── WEB-INF
│   │   ├──lib
|   |   |   ├──poi-ooxml-schemas-4.1.2.jar
|   |   |   |--ooxml-schemas-1.1.jar
```
