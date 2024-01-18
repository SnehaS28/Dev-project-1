# Dev-project-1
In this project, I used storage infrastructure on two Linux servers and implemented a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL as its backend Relational Database Management System (RDBMS).
# Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.


![5021](https://user-images.githubusercontent.com/85270361/210136746-989083fe-d67f-4454-97d5-5f7cc8f921f3.PNG)


1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. [Database](https://www.computerhope.com/jargon/d/database-server.htm) Server or File System Server such as [FTP](https://titanftp.com/2022/07/05/what-is-an-ftp-server/) server, or [NFS](https://www.techtarget.com/searchenterprisedesktop/definition/Network-File-System) Server.


# 3-Tier Setup
1. A Laptop or PC to serve as a client.
2. An EC2 Linux Server as a web server (This is where you will install WordPress).
3. An EC2 Linux server as a database (DB) server.

Using RedHat OS for this project.

