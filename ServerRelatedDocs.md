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

Place this **.htaccess** file in project folder and then paste all the contents of the project folder in  /var/www/html folder

### Step 2

Restart apache service

```bash
sudo service apache2 restart
```

If while refreshing page, shows 404 error follow these extra steps

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

## 3. GoDaddy SSL Certificate Configs

Steps to get .jks file for tomcat from a GoDaddy SSL certificate

From Godaddy SSL, we get three files

1. certfile.crt
2. keyfile.key
3. cert_bundle.crt

### Step 1

Run the following command

```bash
openssl pkcs12 -export -in /dirname/certfile.crt 
-inkey /dirofkeyfile/keyfile.key -out /destdir/filename.p12 
-name 'aliasname' -CAfile /dirname/cert_bundle.crt 

```

### Step 2

Use the generated .p12 file to generate .jks file using java keytool

```bash
keytool -importkeystore 
-deststorepass "password" 
-destkeypass "password" 
-destkeystore /destdir/filename.jks 
-srckeystore /srcdir/filename.p12 
-srcstoretype PKCS12 
-srcstorepass "password" 
-alias "aliasname"
```

Edit the server.xml file in tomcat as required

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
                <Certificate certificateKeystoreFile="/dirname/filename.jks"
                        keystorePass="password"
                        clientAuth="false" 
                        sslProtocol="TLS"
                        keyAlias="aliasname"
                        type="RSA"/>
        </SSLHostConfig>
    </Connector>
```
