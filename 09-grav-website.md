# Grav Website

## Install Grav

### Navigate to your website

```bash
cd /var/www/mywebsite
```

### Get Grav and unzip

```bash
wget https://getgrav.org/download/core/grav-admin/latest -O grav.zip
unzip grav.zip && mv grav-admin/* .
rm -rf grav.zip grav-admin
```

### Change permissions

```bash
chown -R www-data:www-data /var/www/mywebsite/
```

### Activate apache rewrite


```bash
sudo a2enmod rewrite
```

### Edit `.htaccess`

```bash
sudo vim /var/www/example/.htaccess
```

### Add this content

```html
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule .* index.php [L]
</IfModule>
```

### Restart Apache

```bash
sudo systemctl restart apache2
```

### Done

Access Grav by www.mywebsite.com/admin and create username and password.

### Useful: Allow Your User to Edit Files Without Issues

If your user is youruser, add them to the web group:

```bash
sudo usermod -aG www-data youruser  # On Ubuntu/Debian
```

Then, restart your session or run:

```bash
su - youruser
```

Now, you can edit files using:

sudo nano /var/www/mywebsite/index.php
