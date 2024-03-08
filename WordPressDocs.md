# WORDPRESS DOCS

## 1. Setting up of Email SMTP Mailer

To configure SMTP mailer follow the steps

### Step 1

For Wordpress based Websites, log in to the cPanel or WHM interface

#### Config 1

Go to "Server Configuration" and then Click on "Tweak Settings"

![server config image](<assets/Screenshot (90).png>)


Then set "Restrict outgoing SMTP to root,exim and mailman" to "Off"
![smtp option image](<assets/Screenshot (91).png>)


#### Config 2

Go to "Security Center" and Click on "SMTP Restrictions"

![security center image](<assets/Screenshot (93).png>)

Disable the option

![disable option image](<assets/Screenshot (94).png>)

#### Note

If you have done the "Config 1" first then no need to do "Config 2" as it will be automatically disabled.



### Step 2

Add any well known Mail plugin from Plugins option by Logging in to WordPress Admin Console

Configure by setting "Other SMTP" menu from the plugin page

Provide the necessarry informations

1. STMP server address (ex: smtp.secureserver.com)
2. PORT details for SMTP (No SSL: 80, SSL: 465, TLS: 587)
3. Email Address which you want to use (ex: ```**info@domain.in**```)
4. Password of the Email Address you want to use

#### Note: SMTP address and PORT will be available in server configuration options of respective professional email provider console page

Test the mailer by sending a test mail from the plugin page options
