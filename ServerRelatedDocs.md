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

Place this **.htaccess** file in project folder and then paste all the contents of the project folder in /var/www/html folder

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

## 3. Deploy a React App with a particular context Path

If a requirement arises that in same apache server it is required to host multiple seperate react projects, then it is needed to deploy it in seperate context paths.

For Example, a project is needed to be hosted with context path **"/DEF"**
which means it will be accessible as **"www.domain.com/DEF"**, then follow the below steps

### Step 1

go to 000-default.conf or the virtual host conf file situated in the following folder(for apache)

```bash
/etc/apache2/sites-available/000.default.conf
```

Edit the file according to the requirement

```xml
<VirtualHost *:80>

    Alias "/DEF" "/var/www/build"

    <Directory "/var/www/build">
        Require all granted
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All

    </Directory>

    # Alias for static files in React build folder
    Alias "/DEF/static" "/var/www/build/static"

    <Directory "/var/www/build/static">
        Require all granted
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all


        RewriteEngine On
        RewriteBase /DEF/

        RewriteRule ^static/(.*)$ /var/www/build/$1 [L]
    </Directory>

    ....other configs
</VirtualHost>
```

**"/var/www/build"** : The path to react build folder which is generated after running the command **"npm run build"** in the react project

### Step 2

add these configs to .htaccess file which will be placed inside the build folder or the react root directory

```htaccess
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /DEF/
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /DEF/index.html [L]
</IfModule>
```

### Step 3

Now before building the react project and placing the build folder in apache, we need to configure the react project so that the project is setup to be deployed in specified context path, in this case **"/DEF"**

Configure all the routes which is defined in the react project so that all the routes are configured with the context path "/DEF"
For this fo to **"index.js"** file present in the react project and configure the **"BrowserRouter"** component as follows

```javascript
root.render(
  <React.StrictMode>
    <BrowserRouter basename="/DEF">
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

Now in root project folder of recact project create a **".env"** file and place the following content

```javascript
PUBLIC_URL=/DEF
```

Now run the build command **"npm run build"** to build the react project.
Here the project will be build so that it will be served as **"/DEF"** like **"www.domain.com/DEF"**

### Step 4

Now place the build folder in apache directory **"/var/www/"** and after completing the Step 1 and 2, restart the apache service

```bash
sudo service apache2 restart
```

## 4. Security Practices for WebApps deployed in Apache

### 1. Server Name Disclosure and Security Headers

While opening any web app and if we check the **Network** tab from any browser and see the Response Headers, there is a value **"Server"**
For best practices, one should hide the server details so that attackers will not know the server details as getting hold of server details will allow the attackers to exploit the server features.

It is also required to add some required security headers with some recommended values.

### Step 1

Install the mod security module

```bash
apt-get install libapache2-mod-security2
a2enmod security2
```

perform this operation

```bash
cp /etc/modsecurity/modsecurity.conf-recomended  /etc/modsecurity/modsecurity.conf
```

Navigate to the location

```bash
cd /etc/modsecurity/
```

Edit the file **"modsecurity.conf"**

```bash
vi modsecurity.conf
```

Set the following values in the file

```bash
SecRuleEngine DetectionOnly
SecStatusEngine On
SecServerSignature "Any Text"  # write anything as per the requirement
```

### Step 2

Navigate to the following path

```bash
cd /etc/apache2
```

Now Edit the file **"apache2.conf"** and place the following which contains all the recommended security headers and the server signature

```bash
<IfModule mod_headers.c>

  # Set headers
  Header set X-Frame-Options "DENY"
  Header set X-XSS-Protection "0"
  
  Header set Referrer-Policy "strict-origin-when-cross-origin"
  

  # Strict-Transport-Security (requires extra configuration)
  Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"

  # Access-Control-Allow-Origin (specific origin, adjust as needed)
  Header set Access-Control-Allow-Origin "ipaddress" # provide the server ip address

  # Other CORS headers (adjust values as needed)
  
  Header set Cross-Origin-Embedder-Policy "require-corp"
  Header set Cross-Origin-Resource-Policy "same-site"

  # X-DNS-Prefetch-Control
  Header set X-DNS-Prefetch-Control "off"

</IfModule>

#ServerTokens Prod
#ServerSignature Off
SecServerSignature "<Any Text>" # write anything as per the requirement
<IfModule security2_module>
    SecRuleEngine on
   # ServerTokens Prod
    SecServerSignature "<Any Text>" # write anything as per the requirement
</IfModule>

#for allowing specific mime types

<IfModule mod_mime.c>
    AddType application/javascript .js
    AddType text/css .css
</IfModule>
```

### 2. Block Unwanted HTTP Methods

The WebApps deployed will only access GET and POST HTTP methods and will not allow other options such as OPTIONS.

### Step 1
Add these lines to .htaccess file which is placed in root directory of the project (build folder for react)

```bash
<IfModule mod_rewrite.c>
    # Block OPTIONS requests

    RewriteCond %{REQUEST_METHOD} OPTIONS
    RewriteRule .* - [R=405,L]

    # Allow only GET and POST requests

    RewriteCond %{REQUEST_METHOD} !^(GET|POST)$
    RewriteRule .* - [R=405,L]
</IfModule>
```

Navigate to the following path

```bash
cd /etc/apache2/sites-available
```

Edit the **"000-default.conf"** and place the following lines inside the existing **"Directory"** block

```bash
<Directory /var/www/html>
    Options Indexes FollowSymLinks MultiViews
    AllowOverride All
    Require all granted

    RewriteEngine On

    # Block OPTIONS requests

    RewriteCond %{REQUEST_METHOD} OPTIONS
    RewriteRule .* - [R=405,L]

    RewriteCond %{REQUEST_METHOD} !^(GET|POST)$
    RewriteRule .* - [R=405,L]


    <IfModule mod_headers.c>
        Header always set Allow "GET, POST"
        <LimitExcept GET POST>
            Require all denied
        </LimitExcept>
    </IfModule>

</Directory>
```

Restart apache service

```bash
service apache2 restart
```
