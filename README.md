# 3 - Tier Architecture in Networking 🌐
 
## Table of Contents 📚
1. [Tier 1 – Presentation Tier Setup (nginx)](#tier-1--presentation-tier-setup-nginx) 🖥️
2. [Tier 2 – Application Tier Setup (apache2)](#tier-2--application-tier-setup-apache2) 🚀
3. [Tier 3 – Setup Data Tier (MariaDB and Secure phpMyAdmin)](#tier-3--setup-data-tier-mariadb-and-secure-phpmyadmin) 🛡️
4. [Connect Tier 3 (Data Tier) to Tier 2 (Application Tier)](#connect-tier-3-data-tier-to-tier-2-application-tier) 🔗
5. [Importing Database](#importing-database) 📥
6. [Setup Network File Sharing (NFS)](#setup-network-file-sharing-nfs) 📂
7. [Documentation](#documentation) 📖

## Tier 1 – Presentation Tier Setup (nginx) 🌐

1. Install nginx:
   ```bash
   sudo apt-get install nginx -y
   ```

2. Check Nginx status:
   ```bash
   sudo systemctl status nginx
   ```

3. Check if the nginx website is working by typing localhost in your web browser. 🌐

4. Reverse proxy nginx (put host’s IP):
   ```bash
   sudo nvim /etc/nginx/sites-available/default
   ```
   Add the following configuration:
   ```nginx
   location /var/www/softdev_project.com {
       proxy_pass http://192.168.132.139;
       proxy_buffering off;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-Host $host;
       proxy_set_header X-Forwarded-Port $server_port;
   }
   ```

5. Add your domain to /etc/hosts:
   ```bash
   sudo nvim /etc/hosts
   ```

6. Create a configuration file for your domain at /etc/nginx/conf.d/:
   ```bash
   sudo nvim /etc/nginx/conf.d/softdev_project.com.conf
   ```
   Add the following configuration:
   ```nginx
   upstream softdev-project.com {
       server 192.168.132.139 fail_timeout=0;
       server 192.168.132.149 backup;
   }

   server {
       listen 80;
       server_name StudentResultManagementSystem.edu.ph;

       location / {
           proxy_pass http://softdev-project.com$1;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header Host $host;
           proxy_redirect off;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_connect_timeout 90;
           proxy_send_timeout 90;
           proxy_read_timeout 90;
           client_max_body_size 10m;
           client_body_buffer_size 128k;
           proxy_buffer_size 4k;
       }
   }
   ```

7. Restart nginx:
   ```bash
   sudo systemctl restart nginx
   ```

## Tier 2 – Application Tier Setup (apache2) 🚀

1. Install apache2:
   ```bash
   sudo apt install apache2
   ```

2. Add 'Apache' and 'Apache Full' to the firewall list:
   ```bash
   sudo ufw allow 'Apache'
   sudo ufw allow 'Apache Full'
   ```

3. Check Apache website.

4. Setup Virtual Host (/var/www/softdev_project.com):
   ```bash
   sudo mkdir /var/www/softdev_project.com
   sudo chown -R USER:USER /var/www/softdev_project.com
   sudo chmod -R 755 /var/www/softdev_project.com
   ```

5. Permissions and setup PHP web.

6. Configure .conf file (/etc/apache2/sites-available/):
   ```bash
   sudo nvim /etc/apache2/sites-available/softdev_project.com.conf
   ```
   Add the following configuration:
   ```apacheconf
   <VirtualHost *:80>
       ServerAdmin webmaster@localhost
       ServerName softdev_project.com
       ServerAlias www.softdev_project.com
       DocumentRoot /var/www/softdev_project.com
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

7. Enable the new .conf file and disable 000-default.conf:
   ```bash
   sudo a2ensite softdev_project.com.conf
   sudo a2dissite 000-default.conf
   ```

8. Test the new PHP website.

## Tier 3 – Setup Data Tier (MariaDB and Secure phpMyAdmin) 🛡️

1. Install MySQL:
   ```bash
   sudo apt-get install mysql-server
   ```

2. Install PHP and PHP extensions:
   ```bash
   sudo apt-get install php php-cgi libapache2-mod-php php-mbstring php-all-dev
   ```

3. Install PHP MySQL extension:
   ```bash
   sudo apt-get install php8.1-mysql
   ```

4. Install MariaDB:
   ```bash
   sudo apt install mariadb-server php-mysql
   ```

5. Set up MySQL.

6. Configure MySQL.

7. Alter the user and flush privileges.

8. Access MySQL again.

9. Test localhost/phpMyAdmin.

## Connect Tier 3 (Data Tier) to Tier 2 (Application Tier) 🔗

1. Whitelist the IP address of Tier 2 (Application Tier) to Tier 3 (Data Tier).

2. Create a new user for the remote database.

3. Change "bind-address = 127.0.0.1" to "bind-address = 0.0.0.0".

## Importing Database 📥

1. Access and show the database by logging into your remote account.

2. Create, import, and check table database.

3. Change the configuration of the PHP website database (change to Tier 3 database credentials).

4. Add include apache.conf.

5. Test the website.

## Setup Network File Sharing (NFS) 📂

1. Setup components (host side and client side).

2. Create the shared directories on the host.

3. Configure the NFS Exports on the Host server.

4. Adjust the firewall on the host.

5. Create mount points and mount directories on the client.

6. Check if directories are mounted.

7. Test NFS access.

8. Mount the remote NFS directories at boot.

## Documentation 📖

This documentation provides a step-by-step guide to setting up a multi-tiered application environment using Nginx, Apache2, MariaDB, and NFS file sharing. Follow the instructions in each section to set up your environment successfully. 🚀🌐🛡️🔗📥📂
