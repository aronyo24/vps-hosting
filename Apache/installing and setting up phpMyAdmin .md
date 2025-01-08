# process of installing and setting up phpMyAdmin

### Install Apache, PHP, MySQL, and Required Extensions
```sh
   sudo apt install apache2 libapache2-mod-php php mysql-server php-mysql
```
### Install phpMyAdmin

```sh
   sudo apt install phpmyadmin
```
1.You will be asked to choose the web server. Select Apache2 using the space bar and then press Enter.

2.When prompted to configure the database for phpMyAdmin, choose "No".

### Check if the Link Already Exists

```sh
ls -l /etc/apache2/conf-available/phpmyadmin.conf
```
### Enable the Configuration
```sh
   sudo a2enconf phpmyadmin
```
### Restart Apache
```sh
   sudo systemctl restart apache2

```

## Secure phpMyAdmin

### Disable root login from phpMyAdmin

```sh
   sudo mysql
```

### Run the following SQL commands:

```sh
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your-password';
FLUSH PRIVILEGES;
EXIT;

```
