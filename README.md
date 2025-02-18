# Drupal Project Setup with DDEV and Nginx

## 1. Generate SSH Key On Server
```sh
ssh-keygen -t rsa
```

## 2. Install Git
```sh
sudo yum install git
```

## 3. Install Docker
```sh
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
newgrp docker
```

## 4. Install DDEV
```sh
curl -fsSL https://raw.githubusercontent.com/ddev/ddev/master/scripts/install_ddev.sh | bash
```

## 5. Setup Project
```sh
ddev config
ddev start
ddev composer install
ddev drush site:install standard --account-name=admin --account-pass=admin
```

## 6. Import Config
```sh
cd web/sites/default/
nano settings.php
```
Add the following line in `settings.php`:
```php
$settings['config_sync_directory'] = '../config';
```

## 7. Set the UUID and Delete Shortcut
```sh
ddev drush config:get system.site uuid # Run on local
ddev drush config:set system.site uuid <local-uuid> --yes
ddev drush entity:delete shortcut
```

## 8. Install Nginx
```sh
sudo yum install -y nginx
cd /etc/nginx/
sudo mkdir sites-available
sudo mkdir sites-enabled
sudo ln -s /etc/nginx/sites-available/demoevents.tejasvaij.com /etc/nginx/sites-enabled/
```
Modify `nginx.conf` to include `sites-enabled`.

## 9. Write Nginx Block for HTTP
```sh
cd sites-available/
sudo nano demoevents.tejasvaij.com
```
Add content from `https://demoevents.tejasvaij.com.nginx`.

## 10. Start Nginx Service
(Ensure that services running on port 80 are stopped before proceeding.)
```sh
sudo systemctl start nginx
sudo systemctl enable nginx
```

## 11. Point Domain to the IP Address
Modify the `server_name` in the Nginx block from IP address to domain name.

## 12. Install SSL Certificate
```sh
sudo yum install -y certbot python3-certbot-nginx
sudo certbot --nginx -d demoevents.tejasvaij.com
```

## 13. Change Port Number
Update the Nginx block to use port 443 for `web` in the DDEV container:
```sh
sudo systemctl restart nginx
```

## 14. Setup Auto-Renewal for SSL
```sh
sudo certbot renew --dry-run
```

This completes the setup process for deploying a Drupal site with DDEV and Nginx.

