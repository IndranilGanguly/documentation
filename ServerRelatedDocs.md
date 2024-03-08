# SERVER RELATED DOCS



## 1. Node JS Installation

### Step 1

In Linux Machine, Run the following command

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh|bash 
```

### Step 2

To check nvm version

```bash
nvm --version
```

### Step 3

List the available Node Versions

```bash
nvm list-remote
```

### Step 4

Install Required Node Version

```bash
nvm install "version-name"
```

"version-name" will be the version number which will be available by running the command in Step 3


## 2. Deploy a React App

### Step 1

Create a file with the following contents and save it as **.htaccess**

Old .htaccess

```htaccess
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-l
  RewriteRule .  /index.html [L]
</IfModule>
```

New .htaccess

```htaccess
Options -MultiViews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.html [QSA,L]
```

Place this **.htaccess** file in project folder and then paste the contents of the project folder in  /var/www/html folder

### Step 2

Restart apache service

```bash
sudo service apache2 restart
```

If while refreshing page it shows 404 error follow these extra steps

### Step 1

go to 000-default.conf file situated in the following folder

```bash
/etc/apache2/sites-available/000.default.conf
```

Edit the file according to the requirement

```xml
<VirtualHost *:80>
    <Directory /var/www/html>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    ....other configs
</VirtualHost>


<VirtualHost *:443>
    <Directory /var/www/html>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    .... other configs
</VirtualHost>
```

### Step 2

Run the following command

```bash
sudo a2enmod rewrite
```


### Step 3

Restart apache service

```bash
sudo service apache2 restart
```



## 3. GoDaddy SSL Certificate

Steps to get .jks file for tomcat from a GoDaddy SSL certificate


### Step 1

Run the following command

```bash
openssl pkcs12 -export -in /etc/apache2/ssl/9bd0e395feb19d80.crt 
-inkey /etc/apache2/ssl/agor_ssl.key -out /opt/tomcat/ssl/agor_ssl.p12 
-name tomcat -CAfile /etc/apache2/ssl/gd_bundle-g2-g1.crt 

(Password: 'password')
```

### Step 2

Use the generated .p12 file to generate .jks file using java keytool

```bash
keytool -importkeystore 
-deststorepass changeit 
-destkeypass "password" 
-destkeystore /opt/tomcat/ssl/agor.jks 
-srckeystore /opt/tomcat/ssl/agor_ssl.p12 
-srcstoretype PKCS12 
-srcstorepass "password" 
-alias "tomcat"
```

Edit the server.xml file in tomcat as required

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
                <Certificate certificateKeystoreFile="/opt/tomcat/ssl/agor.jks"
                        keystorePass="password"
                        clientAuth="false" 
                        sslProtocol="TLS"
                        keyAlias="tomcat"
                        type="RSA" />
        </SSLHostConfig>
    </Connector>
```



