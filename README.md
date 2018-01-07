# Linux server configuration

## Bike catalog app

This project teaches you to secure and set up a Linux server. 
The project is running on [Amazon Lightsail](https://portal.aws.amazon.com/billing/signup). 
Currently the app is running on [http://35.159.1.90/](http://35.159.1.90/).


## Setup Amazon Lightsail instance

### First, log in to Lightsail. 
If you don't already have an [Amazon Web Services](https://portal.aws.amazon.com/billing/signup) 
account, you'll be prompted to create one.

### Create an instance.
Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. 
A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.


### Choose an instance image: Ubuntu
Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating 
system and optionally built-in applications. For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. 
First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.

	
### Choose your instance plan.
The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, 
the lowest tier of instance is just fine. And as long as you complete the project within a month and shut your instance down, the price will be zero.


### Give your instance a hostname.
Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. 

### Wait for it to start up.
It may take a few minutes for your instance to start up.

### It's running.
Once your instance has started up, you can log into it with SSH from your browser.
The public IP address of the instance is displayed along with its name. Explore the other tabs of the user interface to find the 
Lightsail firewall and other settings. 

When you SSH in, you'll be logged as the ubuntu user. When you want to execute commands as `root`, you'll need to use the `sudo` command to do it.

## 1. Setup your new Linux Server

Connect to your server via Amazon terminal and update all currently installed packages.

	$ sudo apt-get update
	$ sudo apt-get updrade
	
Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
	
	$ sudo nano /home/your_username/.ssh/ssh_config
		
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
	
	$ ufw allow 2200/tcp
	$ ufw allow 80/tcp
	$ ufw allow 123/tcp
	$ ufw enable	
		
> *Warning*: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. 
> When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. 
> The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. 

Download Lightsail default key from your account page.

Create folder and save the key content.
	
	$ mkdir /home/your_username/.ssh/lightsailkey
	
Generate new key pair on your *LOCAL* machine.
	
	$ ssh keygen
	
Change the permissions to secure the key folder.

	$ chmod 700 key
	
Change the permissions to secure the key.
	
	$ chmod 600 .ssh/authorized_keys
	
Read content on local machine and copy it

	$ cat .ssh/key.pub
	
Connect to the server from your terminal

	$ ssh -i ~/.ssh/key -p2200 ubuntu@35.159.1.90
	
Create a new user account named `grader`.

	$ sudo adduser grader
	
Give grader the permission to sudo.

	$ usermod -aG sudo grader
	
Connect to grader user

	$ sudo su -grader
	

Create an SSH key pair for grader and secure it.

	$ mkdir .ssh
	$ chmod 700 .ssh
	$ touch .ssh/authorized_keys
	$ chmod 600 .ssh/authorized_keys
	
Open authorized_keys

	$ sudo nano .ssh/authorized_keys
	
Paste key content.

Restart ssh service.

	$ sudo service ssh restart

Press `CTRL + D` to exit `grader` user.

Now you can connect to the server as grader user.

	$ ssh -i ~/.ssh/key -p2200 grader@35.159.1.90
	
Press `CTRL + D` to close connection.

Set your machine to UTC.

	$ sudo timedatectl set-timezone UTC

Check time.

	$ date
	
## 2. Setup PostgreSQL

Install PostgreSQL with extra packages

	$ sudo apt-get install postgresql postgresql-contrib
	
Switch to postgres acount

	$ sudo su - postgres

To run SQL commands type

	$ psql

To exit this mode type

	# `\q` or `exit`

Configure remote access to postgres

	$ sudo nano /etc/postgresql/9.5/pg-hba.conf
	
Add database user (role)

	$ sudo su -postgres
	$ create user catalog with password catalog;
	
Check existing roles

	$ postgres@@35.159.1.90:~$ psql
	postgres=# \du





### That's it you are ready to go! Feel free to make any changes to the provided code.


### CONTACT

Please send you feedback to

  max.savin3@gmail.com
