# Deploying Laravel on DigitalOcean with LAMP Stack

This cheatsheet provides step-by-step instructions to deploy and set up a Laravel application on a DigitalOcean Ubuntu droplet with a LAMP stack.

## Prerequisites

- [x] DigitalOcean account with a created droplet (Ubuntu 20.04 recommended).
- [x] Basic server administration skills.
- [x] Git installed on your local machine.
- [x] A Laravel application hosted on GitHub (e.g., "laravel_example_app").

## Initial Server Setup

1. **Create a DigitalOcean Droplet:**
   - Choose Ubuntu 20.04 as the operating system.
   - Follow the DigitalOcean wizard to create the droplet.

2. **Access Your Droplet:**
   - Open your terminal and use SSH to access your droplet:
     ```bash
     ssh root@your_droplet_ip
     ```

3. **Update Server Packages:**
   ```bash
   sudo apt update
   sudo apt upgrade -y # optional
   
4. **Install Apache Web Server**
    ```bash
    sudo apt install apache2
    
    # Start Apache and Enable it.
    systemctl start apache2
    systemctl enable apache2

    # Check status
    sudo systemctl status apache2    
   
5. **Install MySQL:**
    ```bash
    sudo apt install mysql-server
    sudo mysql_secure_installation # modify some insecure defaults

6. **Change root MySQL password:**
    ```bash
    sudo mysql
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
    mysql -u root -p # Access mysql
    
7. **Install PHP and Required Extensions:**
    ```bash
    sudo apt install php libapache2-mod-php php-mysql php-common php-bcmath php-ctype php-json php-mbstring php-openssl php-pdo php-tokenizer php-xml php-zip php-gd
    ```
    - If you want specific version of PHP like 7.4
   ```bash
   sudo apt install php7.4 libapache2-mod-php7.4 php7.4-mysql php7.4-common php7.4-bcmath php7.4-ctype php7.4-json php7.4-mbstring php7.4-openssl php7.4-pdo php7.4-tokenizer php7.4-xml php7.4-zip php7.4-gd

    
8. **Configure Apache for Laravel:**
    - Create an Apache virtual host configuration for your Laravel app (e.g., /etc/apache2/sites-available/laravel_example_app.conf).
    - Configure the virtual host to use PHP for processing PHP files.
    
9. **Create virtual host config**
    ```bash
    sudo nano /etc/apache2/sites-available/laravel_example_app.conf
    ```
    
    ```apache
    # APACHE CONFIG [https://laravel.com/docs/7.x/deployment]
    <VirtualHost *:80>
        ServerAdmin webmaster@your_domain.com
        ServerName your_domain.com
        DocumentRoot /var/www/laravel_example_app/public

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/laravel_example_app/public>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    
10. **Enable the Apache Configuration:**
    - Enable the virtual host:
    ```bash
    sudo a2ensite laravel_example_app.conf
    ```

    - Disable the default virtual host (if needed):
    ```bash
    sudo a2dissite 000-default.conf
    ```

11. **Test Apache Configuration:**
    ```bash
    sudo apache2ctl configtest
    ```

## Deploy Laravel Application

1. **Clone Your Laravel App from GitHub:**
    ```bash
    git clone https://github.com/yourusername/laravel_example_app.git /var/www/laravel_example_app
    ```

2. **Install Composer**    
    - Follow the instructions at [https://getcomposer.org/download/](https://getcomposer.org/download/) to install Composer.

3. **Install Composer Dependencies**
    ```bash
    cd /var/www/laravel_example_app
   composer install --no-interaction --prefer-dist

4. **Generate Laravel Application Key:**
    ```bash
    php artisan key:generate

5. **Set Permissions:**
    - Ensure proper file permissions for Laravel Application
    ```bash
    sudo chown -R $USER:$USER /var/www/laravel_example_app
    sudo chmod -R 755 /var/www/laravel_example_app
    sudo chmod -R 755 /var/www/laravel_example_app/storage


6. **Access MySQL:**
    ```bash
    mysql -u root -p

7. **Create a database and user:**
    ```bash
    CREATE DATABASE laravel_example_app_db;
    CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'your_password';
    GRANT ALL PRIVILEGES ON laravel_example_app_db.* TO 'laravel_user'@'localhost';
    FLUSH PRIVILEGES;

8. **EXIT MYSQL**
    ```bash
    exit

9. **Configure .env**
    - Update the Database Configuration
    ```bash
    cd /var/www/laravel_example_app
    nano .env

    
10. **Migrate and Seed Database (Laravel):**
    ```bash
    php artisan migrate --seed

## Final Steps
1. **Restart Apache**
    ```bash
    systemctl restart apache2
    ```

2. **Restart MySQL**
    ```bash
    systemctl restart mysql
    ```

Your Laravel application should now be deployed and accessible via your domain name or your droplet's IP address through web browser. 
