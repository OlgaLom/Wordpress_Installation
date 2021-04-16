# Install Wordpress on Ubuntu 


### STEP 1: Prepare and Update System

```bash
#update our stystem.
sudo apt get update 
```
### STEP 2: Download Wordpress - Upload Wordpress installation

#### Download Wordpress

```bash
#Change to writable directory and download wordpress
cd /tmp
curl -O https://wordpress.org/latest.tar.gz

# extract the file inorder to create the wordpress structure
tar xzvf latest.tar.gz

# Create a dummy .htaccess file so that this will be available for wordpress to use later
touch /tmp/wordpress/.htaccess

# We’ll also copy over the sample configuration file to the filename that WordPress actually reads:
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php

# Now we can copy the entire contents of the directory into our document root
sudo cp -a /tmp/wordpress/. /var/www/wordpress

```
<br>

#### Upload Wordpress installation

Connect to the server via a ssh client. I will use the Bitvise.

```bash
# Navigate to propper directory and create a folder
cd var/www/

# upload zip file and unzip it
sudo unzip MyZipFile.zip -d /var/www/MydestinationFolder

# Remove the zip file
sudo rm MyZipFile.zip
```
<br>

### STEP 3: Configure wordpress directory

```bash
#Change ownership of the folder
sudo chown -R www-data:www-data /var/www/MyWorkingFolder

# Set perimissions for files and directories
sudo find /var/www/MyWorkingFolder/ -type d -exec chmod 750 {} \;
sudo find /var/www/MyWorkingFolder/ -type f -exec chmod 664 {} \;

```
<br>

### STEP 4: Create Database for our site

```bash
#Connect to mysql
sudo mysql -u root
```

```sql
# Create a database 
CREATE DATABASE MyDataBaseName DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
# If everything went ok we will get the answer
# Query OK, 1 row affected, 2 warnings (0.01 sec)

#Create user for the database
CREATE USER 'MyUser'@'localhost' IDENTIFIED BY 'Password';

#Grand privilages to user for the database that we have created
GRANT ALL ON MyDataBaseName.* TO 'MyUser'@'localhost';

# We need to flush the privileges so that the current instance
# of MySQL knows about the recent changes we’ve made
FLUSH PRIVILEGES;

```
<br>

### STEP 5: Connect to the Database via MYSQL Workbench (Skip if you creating new installation)

Connect to database via client [MysqlWorkbench]

Click on the plus sign and add connection, insert the bellow 

| 		Parameter   	| Option 													|
| ----------------------| ------------------------------------------------------- 	|
| Connection Name: 		| Label Name for mysqlworkbench 							|
| Connection Method:  	| Standard TCP/IP over SSH   								|
| SSH Hostname:   		| your Hostname  											|
| SSH Username:  		|  name of the user that you use to connect to the server 	|
| SSH Password:  		| user password  											|
| SSH key File:  		|  ssh private key if you use one 							|
| MySQL Hostname:  		| 127.0.0.1  												|
| MySQL Server Port   	|  3306														|
| Username:  			| Database user  											|
| Password:  			|  Database password										|
| Default Schema:  		| Database Name  											|

For moving a wordpress site follow the bellow step 

we must insert the backup sql file but before that we must edit the sql file in order to change the domain name if we have new.

In the sql file find <code>siteurl</code> copy that parameter and then find and replace all the instanaces tat jave this value.

after that go to mysqlWorkbench and import the sql file 
<br><br>
►From the left  sidebar click on <code><strong> Data Import/Restore</strong></code>
<br><br>
►Select<code><strong>Import from Self-Contained File</strong></code> and search for your sql file <br>
<br>
►Set the <code><strong>Default Target Schema</strong></code>to your database

<br>

### STEP 6: Insert Database Credentials
 

Back to the installation folder edit the <code> wp-config.php</code> and insert your data

```php
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'insert-database-name-here' );

/** MySQL database username */
define( 'DB_USER', 'insert-database-user-here' );

/** MySQL database password */
define( 'DB_PASSWORD', 'insert-database-password-here' );

```
<br>

### STEP 7: Create Apache’s Configuration

```bash
# Navigate to apaches conf files
cd /etc/apache2/sites-available/

# Create conf file for our site
sudo vi mydomain.conf

```
<br>
Content of conf file:

```apacheconf 
<VirtualHost  *:80>
    ServerName  MyDomain.gr
    ServerAlias www.MyDomain.gr
    #ServerAdmin webmaster@localhost
    DocumentRoot /var/www/MyPath
    <Directory /var/www/MyPath/>
        AllowOverride All
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
<br>
Save and exit. 
After that set we must enable the configuration file and enable the mod rewrite

```bash
# After the file creation make sure tha you have rewrite module enable
sudo a2enmod rewrite

# Enable the config file 
sudo a2ensite mydomain.conf

# Reload apache
sudo systemctl reload apache2
```
### STEP 8: Insert a Certificate for the domain

We will use Certbot to insert certificate to our domain.<br>
https://certbot.eff.org/lets-encrypt/ubuntufocal-apache

```bash
#Ensure that your version of snapd is up to date
sudo snap install core; sudo snap refresh core

#Install Certbot
sudo snap install --classic certbot

#Prepare the Certbot command
sudo ln -s /snap/bin/certbot /usr/bin/certbot

#Run Certbot for apache site
sudo certbot --apache

```
<br>

### STEP 9: Log in to wordpress

Connect to wordpres and navigate to <code> Settings→Permalinks</code> and hit save.



