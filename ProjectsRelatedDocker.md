# Want to run 3 seperate websites from a single VPS server having a single public ip

# Method 1: using context paths

For example lets assume websites have 3 different index.html only representing 3 different websites

For each create a Dockerfile as shown

```dockerfile
# Use official Apache image
FROM httpd:alpine

# Copy your HTML into the Apache web root
COPY index.html /usr/local/apache2/htdocs/

# Expose port 80
EXPOSE 80
```

The above Dockerfile will instruct to install official apache image
then copy the index.html file to its root path and expose port 80 as its own container port


Then for each Dockerfile create the docker image

```bash
docker.build -t app1-img .

docker.build -t app2-img .

docker.build -t app3-img .
```

This creates three seperate docker images

Now run the containers for the images

```bash
docker run -d -p 8081:80 --name app1-cont app1-img

docker run -d -p 8082:80 --name app2-cont app2-img

docker run -d -p 8083:80 --name app3-cont app3-img
```

Here each of the web apps are running on three different ports locally
That is

http://localhost:8081 // for app1

http://localhost:8082 // for app2

http://localhost:8083 // for app3


Now to expose this over the internet and accessible through single IP as context paths

go to the configuration file of the Apache installed in the VPS

```cmd
nano /etc/apache2/sites-available/000-default.conf
```

Mention the following lines in VirtualHost Tag

```apache
ProxyPass "/app1" "http://localhost:8081"
ProxyPassReverse "/app1" "http://localhost:8081"

ProxyPass "/app2" "http://localhost:8082"
ProxyPassReverse "/app2" "http://localhost:8082"

ProxyPass "/app3" "http://localhost:8083"
ProxyPassReverse "/app3" "http://localhost:8083"
```

When user will hit http://<your-ip>/app1,
it will be proxied to http://localhost:8081

When user will hit http://<your-ip>/app2,
it will be proxied to http://localhost:8082

When user will hit http://<your-ip>/app3,
it will be proxied to http://localhost:8083

Save the conf file and enble the proxy settings of apache

```bash
sudo apt update
sudo apt install apache2
sudo a2enmod proxy
sudo a2enmod proxy_http
```

Then restart the apache service

```cmd
service apache2 restart
```

# Method 2: using sub-domains

The steps involving the dockerization is same as for Method 1

Point the subdomains to the VPS ip address

In your Domain provider console DNS settings add **A records**

| Subdomain | Type | Value           |
|-----------|------|-----------------|
| app1.yourdomain.com   | A    | your ip address |
| app2.yourdomain.com  | A    | your ip address |
| app3.yourdomain.com     | A    | your ip address |


Enable the required modules

```bash
sudo apt update
sudo apt install apache2
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod rewrite
sudo service apache2 restart
```

Create a seperate config for app1

```bash
sudo nano /etc/apache2/sites-available/app1.conf
```

use the following

```apache
<VirtualHost *:80>
    ServerName app1.yourdomain.com

    ProxyPreserveHost On
    ProxyPass / http://localhost:8081/
    ProxyPassReverse / http://localhost:8081/
</VirtualHost>
```

do the same for app2.conf and app3.conf


```apache
<VirtualHost *:80>
    ServerName app2.yourdomain.com

    ProxyPreserveHost On
    ProxyPass / http://localhost:8082/
    ProxyPassReverse / http://localhost:8082/
</VirtualHost>
```

```apache
<VirtualHost *:80>
    ServerName app3.yourdomain.com

    ProxyPreserveHost On
    ProxyPass / http://localhost:8083/
    ProxyPassReverse / http://localhost:8083/
</VirtualHost>
```

Enable the configs

```bash
sudo a2ensite app1.conf
sudo a2ensite app2.conf
sudo a2ensite app3.conf

sudo systemctl reload apache2
```

