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
