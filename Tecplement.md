# To deploy the application in both monolithic and microservices architecture on AWS using EC2 instances, follow the steps below:

Prerequisites:
AWS account
AWS CLI installed and configured
SSH key pair for accessing EC2 instances
Monolithic Architecture:
Launch an EC2 Instance:

Instance Type: t2.micro
AMI: Ubuntu
Security Group: Allow HTTP (port 80), HTTPS (port 443), and SSH (port 22)
Connect to the EC2 Instance:

sh
Copy code
ssh -i /path/to/your-key.pem ubuntu@your-ec2-public-dns
Update and Install Dependencies:

sh
Copy code
sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php mysql-server -y
Download and Install WordPress:

sh
Copy code
wget -c http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
sudo rsync -av wordpress/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
Configure MySQL:

sh
Copy code
sudo mysql_secure_installation
# Follow the prompts to set up MySQL

sudo mysql -u root -p
# Create the database and user for WordPress
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Configure WordPress:

Copy wp-config-sample.php to wp-config.php and update database settings:
sh
Copy code
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
sudo nano /var/www/html/wp-config.php
# Update DB_NAME, DB_USER, DB_PASSWORD, DB_HOST to match your MySQL configuration
Restart Apache:

sh
Copy code
sudo systemctl restart apache2
Access WordPress:

Open your browser and navigate to your EC2 instance's public DNS to complete the WordPress setup.
Microservices Architecture:
Launch Two EC2 Instances:

Instance 1 (WordPress): t2.micro, AMI: Ubuntu, Security Group: Allow HTTP (port 80), HTTPS (port 443), and SSH (port 22)
Instance 2 (MySQL): t2.micro, AMI: Ubuntu, Security Group: Allow MySQL (port 3306), and SSH (port 22)
Connect to the MySQL EC2 Instance:

sh
Copy code
ssh -i /path/to/your-key.pem ubuntu@your-mysql-ec2-public-dns
Install MySQL:

sh
Copy code
sudo apt update
sudo apt install mysql-server -y
Configure MySQL:

sh
Copy code
sudo mysql_secure_installation
sudo mysql -u root -p
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'%';
FLUSH PRIVILEGES;
EXIT;
Edit MySQL config to allow remote connections:

sh
Copy code
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
# Comment out bind-address line:
# bind-address = 127.0.0.1
Restart MySQL:

sh
Copy code
sudo systemctl restart mysql
Connect to the WordPress EC2 Instance:

sh
Copy code
ssh -i /path/to/your-key.pem ubuntu@your-wordpress-ec2-public-dns
Install Apache, PHP, and WordPress:

sh
Copy code
sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php -y
wget -c http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
sudo rsync -av wordpress/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
Configure WordPress:

Copy wp-config-sample.php to wp-config.php and update database settings:
sh
Copy code
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
sudo nano /var/www/html/wp-config.php
# Update DB_NAME, DB_USER, DB_PASSWORD, and set DB_HOST to your MySQL EC2 instance's private IP address
Restart Apache:

sh
Copy code
sudo systemctl restart apache2
Access WordPress:

Open your browser and navigate to your WordPress EC2 instance's public DNS to complete the WordPress setup.
Security Group Configuration:
For both setups, ensure the security groups are correctly configured to allow traffic on necessary ports.
Monolithic: HTTP (80), HTTPS (443), and SSH (22)
Microservices:
WordPress Instance: HTTP (80), HTTPS (443), and SSH (22)
MySQL Instance: MySQL (3306) and SSH (22)
Create a Welcome Page:
Log into the WordPress admin dashboard.
Create a new page named "Welcome."
Set this page as the homepage:
Go to Settings -> Reading
Select "A static page" and choose "Welcome" as the homepage.
This completes the deployment of WordPress in both monolithic and microservices architecture on AWS EC2 instance