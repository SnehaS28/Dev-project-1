# UPDATE THE `/ETC/FSTAB` FILE

The UUID of the device will be used to update the /etc/fstab file;

```
sudo blkid
```

![5031](https://github.com/SnehaS28/Dev-project-1/assets/109750527/5582640b-3c38-456f-bc40-4572746d50c8)


![5032](https://github.com/SnehaS28/Dev-project-1/assets/109750527/db20c513-c06a-43aa-809f-a4a5e15028e5)


```
sudo vi /etc/fstab
```

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![5033](https://github.com/SnehaS28/Dev-project-1/assets/109750527/f0f889b1-fecd-40d6-b8bd-4d97b595e49d)



1. Test the configuration and reload the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

2. Verify your setup by running df -h, output must look like this:

![5034](https://github.com/SnehaS28/Dev-project-1/assets/109750527/c5b79c14-fd4a-4127-960b-5541e55b72ed)


Step 2 — Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of
/var/www/html/.

![5039](https://github.com/SnehaS28/Dev-project-1/assets/109750527/b3767b8a-65b1-4ad7-a553-690e85547bf0)


Step 3 — Install WordPress on your Web Server EC2


1. Update the repository

```
sudo yum -y update
```


2. Install wget, Apache and it’s dependencies

```
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```


3. Start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```


4. To install PHP and it’s depemdencies

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

5. Restart Apache

```
sudo systemctl restart httpd
```

6. Download wordpress and copy wordpress to var/www/html

```
  mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  cp wordpress/wp-config-sample.php wordpress/wp-config.php
  cp -R wordpress /var/www/html/
```


7. Configure SELinux Policies

```
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```

Step 4 — Install MySQL on your DB Server EC2

```
sudo yum update
sudo yum install mysql-server
```

Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and 
enable it so it will be running even after reboot:

```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

Step 5 — Configure DB to work with WordPress

```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

Step 6 — Configure WordPress to connect to remote database.
Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY 
from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

![5034](https://user-images.githubusercontent.com/85270361/210138507-0b3b6372-958b-406a-9672-82f729d26b85.PNG)


1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

2. Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

3. Change permissions and configuration so Apache could use WordPress:

4. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your 
workstation’s IP)

5. Try to access from your browser the link to your WordPress 

http://<Web-Server-Public-IP-Address>/wordpress/
  
![5040](https://github.com/SnehaS28/Dev-project-1/assets/109750527/74966c61-ae06-47b9-a405-612f640a9738)

  
Fill out your DB credentials:
  
![5050](https://github.com/SnehaS28/Dev-project-1/assets/109750527/93aebce0-442d-47d7-b030-c1f474182096)



Login to your Wordpress:
  

![5060](https://github.com/SnehaS28/Dev-project-1/assets/109750527/39c7b64a-945f-4bc0-a339-c2107b777356)



Important: Do not forget to STOP your EC2 instances after completion of the project to avoid extra costs.

THANK-YOU!

  
