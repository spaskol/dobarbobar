# Add site in Apache HHTP

Here I show how to add site in Apache web server, add basic authorization and use duckdns.org

## Add another web site

Below I show how I added  site called "mywebsite"

### Create the website

First I  created the directory for the new website inside "/var/www/" and grant permissions

```bash
sudo mkdir -p /var/www/mywebsite
sudo chown -R www-data:www-data /var/www/mywebsite
sudo chmod -R 755 /var/www/mywebsite
```

Then I created simple "index.html" 

```bash
echo "<h1>Welcome to My Website</h1>" | sudo tee /var/www/mywebsite/index.html
```

Then I created .conf for the new website inside "sites-available" directory.

```bash
sudo nano /etc/apache2/sites-available/mywebsite.conf
```

and added the following content

```conf
<VirtualHost *:80>
    ServerAdmin mywebsite@mywebsite.com
    ServerName mywebsite.duckdns.org
    ServerAlias www.mywebsite.duckdns.org
    DocumentRoot /var/www/mywebsite

    <Directory /var/www/mywebsite>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/mywebsite_error.log
    CustomLog ${APACHE_LOG_DIR}/mywebsite_access.log combined
</VirtualHost>
```

then I enable my new site and restart Apache.

```bash
sudo a2ensite mywebsite.conf
sudo systemctl restart apache2
```

## Add access with user and password

First I checked if my website has "AllowOverride All" property inside Directory tag in my ".conf" file. If you follow the previous chapter it should be present.

```bash
sudo nano /etc/apache2/sites-available/mywebsite.conf
```

```bash
<Directory /var/www/mywebsite>
    AllowOverride All
</Directory>
```

Then I added the following in my ".htaccess" file.

```bash
sudo nano /var/www/mywebsite/.htaccess

AuthType Basic
AuthName "Restricted Area"
AuthUserFile /var/www/mywebsite/.htpasswd
Require valid-user
```

You may need to install "apache2-utils". I skipped this step.

```bash
#sudo apt install apache2-utils
```

Then I created ".htpasswd" using `htpasswd`. You need to replace "myusername" with your custom user name and add the desired password when prompted. Then the file will store your user name and encrypted password.

```bash
sudo htpasswd -c /var/www/mywebsite/.htpasswd myusername
```

Than I restarted Apache and I was able to login with my website using user name and password.

```bash
sudo systemctl restart apache2
```

## Create Let's encrypt certificate

I thought it will be difficult but it was quite easy

### First I Installed "certbot"

After everything was set you just need to instal `certbot`

```bash
sudo apt update
sudo apt install certbot python3-certbot-apache
```

### Then I installed the certificate

I just executed the following command

```bash
sudo certbot --apache -d example.com
```

and I got this:

```bash
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for example.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/example.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/example.com/privkey.pem
This certificate expires on 2025-05-07.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for example.com to /etc/apache2/sites-available/example-le-ssl.conf
Congratulations! You have successfully enabled HTTPS on https://example.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

## Source:
Using ChatGPT to optain the information.


