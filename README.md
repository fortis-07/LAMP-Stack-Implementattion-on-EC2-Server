# LAMP-Stack-Implementattion-on-EC2-Server
This guide provides a step-by-step process to set up a LAMP stack (Linux, Apache, MySQL, PHP) on an AWS EC2 t2.micro instance running Ubuntu 24.04 LTS. A LAMP stack is a widely used open-source solution for hosting dynamic web applications. We will install and configure Apache as the web server, MySQL as the database, and PHP as the scripting language. By the end of this setup, your EC2 instance will be ready to host web applications.

## Prerequisites
1. AWS Free-Tier Account
2. EC2 instance for SSH access
3. Vscode (or another Terminal)
   
### Step 1: EC2 instance Set-up
1. Sign in to the AWS Management Console and navigate to the EC2 Dashboard.
2. Click on **Launch Instance**.
3. Configure the instance:
   - **Name**: Enter a name for your instance (e.g., "LAMP Server").
   - **AMI**: Select Ubuntu 24.04 LTS HVM (64-bit architecture).
   - **Instance type**: Choose t2.micro (eligible for free tier).
   - **Key pair**: Choose an existing key pair or create a new one. Download the `.pem` file.
   - **Security group**: Ensure the following ports are allowed:
     - SSH (port 22) for remote access.
     - HTTP (port 80) for web traffic.
   - Optionally, allow traffic from anywhere.

4. Launch the instance.

![WhatsApp Image 2024-09-12 at 21 21 42_a119f0b7](https://github.com/user-attachments/assets/22f32fc0-c956-4648-8794-61e721c0bf1b)



### Step 2: Connect to the EC2 Instance
1. Open a terminal or use VS Code’s terminal.
2. Connect to your instance via SSH:

    ```bash
    cd /path/to/your-key.pem
    ssh -i your-key.pem ubuntu@<your-ec2-public-ip>
    ```
    

![WhatsApp Image 2024-09-13 at 15 25 30_1c55bf09](https://github.com/user-attachments/assets/4748c8cc-14cf-4c62-af86-e3feffa2d5e0)


### Step 3: Update the Server
Run the following command to update the listof packages in the package manager

```bash

sudo apt update
sudo apt upgrade -y

```
### Step 4: Install Apace using Ununtu's package manager

```bash
sudo apt install apache2
```

Verify that Apache is running as a Service on your OS

```bash
sudo systemctl status apache2
```
![WhatsApp Image 2024-09-12 at 17 39 17_e63ed57b](https://github.com/user-attachments/assets/c51ddddb-960e-42a7-add1-7a97758c02f7)

Confirm Apache is working by running:


```bash
curl http://localhost:80
```

This commands helps us to request our Apache HTTP Server

### Step 5: MySQL Installation
1. Install MySQL

```bash
sudo apt install mysql-server -y
```
2. Secure MySQL installation

```bash
sudo mysql
```
Follow the prompts to set up the root password, remove anonymous users, disallow remote root login, and more.

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
3. Login to MySQL and verify that is working
   
```bash
sudo mysql -p
```
### Step 6: PHP Installation
1. Install PHP and necessary PHP extensions for Apache and MySQL:
```bash
sudo apt install php libapache2-mod-php php-mysql php-cli -y
```
2. Check PHP Version
![WhatsApp Image 2024-09-13 at 15 50 54_78706a9c](https://github.com/user-attachments/assets/d8f22740-56fa-4c06-ac3e-b5b30589bcab)

3.Restart Apache to load PHP

```bash
sudo systemctl restart apache2
```
### Step 7: Set Up Virtual Hosts

1. Create a new directory for your website:

```bash
sudo mkdir /var/www/lampproject
```
2. Assign ownership of the directory with the $USER environment variable,which references the current user.

  ```bash
$ sudo chow -R $USER:USER /var/www/projectlamp
```
 
4. Create a new virtual host file:

```bash
sudo nano /etc/apache2/sites-available/lampproject.conf
```

4. Add the following content to configure the virtual host:

```bash
<VirtualHost *:80>
    ServerName lampproject
    ServerAlias www.lampproject
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/lampproject
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

5. Enable the virtual host:
   
```bash
sudo a2ensite yourwebsite.conf
sudo ls /etc/apache2/sites-available
sudo a2ensite lampproject
sudo a2dissite 000-default
sudo apache2ctl configtest
```
6. Restart Apache Server

```bash
   sudo systemctl reload apache2
```

### Step 8: HTML Script Testing
1. Navigate to the project path

```bash
cd /var/www/lampproject
```
2. Create an index.html file

```bash
touch index.html
```
3. Open and edit the file, then paste the html contents below.

```bash
nano index.html
```

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to Nginx</title>
</head>
<body>
    <h1>Welcome!</h1>
    <p>If you're seeing this page, your  setup is working correctly.</p>
</body>
</html>
```


### Step 9: Enable PHP on the Web Server

```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```

```bash
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```

### Step 10: PHP installation Testing
1. Create a PHP file:

```bash
nano /var/www/lampproject/index.php
```
2. Add the following code:
   
```bash
<?php
phpinfo();
?>
```
3. Open <your-ec2-public-ip>/index.php in your browser to see the PHP information page.

   ![WhatsApp Image 2024-09-13 at 16 22 32_2fea93e3](https://github.com/user-attachments/assets/cb94f104-81b2-4cda-989a-d807f7c57adc)


![WhatsApp Image 2024-09-12 at 21 21 58_c6d769b7](https://github.com/user-attachments/assets/fd3e614e-eb9e-4453-8017-f573a414bbd3)

   
5. Remove PHP info page: For security reasons, delete the PHP info page after testing:


```bash
  rm /var/www/lampproject/index.php
```


   ### Your LAMP stack is now fully configured and ready to host web applications.
