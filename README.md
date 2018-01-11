# Linux server configuration

## Bike catalog app

This project teaches you to secure and set up a Linux server. 
The project is running on [Amazon Lightsail](https://portal.aws.amazon.com/billing/signup). 
Currently the app is running on [http://18.196.38.188/](http://18.196.38.188/).


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
```
$ sudo apt-get update
$ sudo apt-get updrade
```

Setup automatic updates

	$ sudo apt install unattended-upgrades

To enable automatic updates, edit /etc/apt/apt.conf.d/20auto-upgrades and set the appropriate apt configuration options:
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
	
	$ sudo nano /home/your_username/.ssh/ssh_config
		
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
```	
$ ufw allow 2200/tcp
$ ufw allow 80/tcp
$ ufw allow 123/tcp
```
Deny connections on port 22
```
$ ufw deny 22/tcp
$ ufw enable	
```		
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
	
Read content on local machine and copy it.

	$ cat .ssh/key.pub
	
Connect to the server from your terminal.
	
	$ ssh -i ~/.ssh/key -p2200 ubuntu@18.196.38.188
	
Create a new user account named `grader`.

	$ sudo adduser grader
	
Give grader the permission to sudo.

	$ usermod -aG sudo grader
	
Connect to grader user.

	$ sudo su -grader
	
Create an SSH key pair for grader and secure it.
```
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
```
Open authorized_keys

	$ sudo nano .ssh/authorized_keys
	
Paste key content.

Restart ssh service.

	$ sudo service ssh restart

Press `CTRL + D` to exit `grader` user.

Now you can connect to the server as grader user.

	$ ssh -i ~/.ssh/key.pem -p2200 grader@18.196.38.188
	
Press `CTRL + D` to close connection.

Disable `root` login. Open new terminal so you don't lock up yourself and connect to server.

	$ sudo nano /etc/ssh/sshd_config

Change 
```
#  PermitRootLogin yes
```
to
```
# PermitRootLogin no
```
Restart ssh service
```
$ sudo service ssh restart 
```
or 
```
$ /etc/init.d/ssh restart
```

Set your machine to UTC.

	$ sudo timedatectl set-timezone UTC

Check time.

	$ date
	
## 2. Setup [PostgreSQL](https://www.postgresql.org/)

Install PostgreSQL with extra packages.

	$ sudo apt-get install postgresql postgresql-contrib
	
Switch to postgres account.

	$ sudo su - postgres

To run SQL commands type.

	$ psql

To exit this mode type.

	# `\q` or `exit`

Configure remote access to postgres.

	$ sudo nano /etc/postgresql/9.5/pg-hba.conf
	
Add database user (role).
```
$ sudo su -postgres
$ create user catalog with password catalog;
```	
Check existing roles.

	$ postgres@18.196.38.188:~$ psql
	postgres=# \du

Create database.

	postgres=# CREATE DATABASE catalog WITH OWNER catalog;

To connect to DB type

	postgres=# \c catalog

Press `q` to disconnect.

Only let catalog permission to create tables.
```	
postgres=# REVOKE ALL ON SCHEMA catalog FROM public;
postgres=# GRANT ALL ON SCHEMA public TO catalog;
```
	
## 3. Install [GIT](https://github.com/) on server

	$ sudo apt-get install git
	
Set name.

	$ git config --global user.name "your_git_username"
	
Set email.

	$ git config --global user.email "your_git_email"
	
Create folder for git catalog.

	$ sudo mkdir catalog
	
Clone the repository.
```
$ cd /var/www/catalog
$ sudo git clone https://github.com/your_git_username/repo_name.git catalog
```
Create `__init__.py` that will contain the [Flask](http://flask.pocoo.org/) app logic.

	$ sudo nano $ cd /var/www/catalog/catalog __init__.py

Make `git` unaccessable from browser. In your git folder create `.htaccess` file.

	$ sudo nano .htaccess

Save it with:
```
Order allow, deny
Deny from all
```
	
## 4. Setup [Apache](http://httpd.apache.org/)

Install Apache2

	$ sudo apt-get install apache2

Open your browser and visit your server ip `http://ip` If all went well, you'll see a greeting.

Install [mod_wsgi](https://pypi.python.org/pypi/mod_wsgi) for serving Python app via Apache.

	$ sudo apt-get install python-setuptools libapache2-mod-wsgi

Restart Apache.

	$ sudo service apache2 restart
	
Get additional package to serve Flask

	$ sudo apt-get install libapache2-mod-wsgi python-dev

Enable mod-wsgi

	$ sudo a2enmod wsgi
	
## 5. Setup Virtual Environment

Install pip.

	$ sudo apt-get install python-pip

Install virtual environment.

	$ sudo pip install virtualenv
	
> If you have error `locale.Error.unsupported locale setting`, run

	$ export LC_ALL=C
	
Create virtual environment in the last catalog folder.
	
	$ sudo virtualenv catalogenv
	
Activate the environment.

	$ source catalog/bin/activate
	
Install [Flask](http://flask.pocoo.org/).

	$ sudo pip install Flask
	
Test your installation.

	$ sudo python __init__.py
	
You should see:

> Running on http://localhost:5000/ or  http://localhost:127.0.0.1:5000/

Deactivate the environment.

	$ deactivate
	
Configure and enable Virtual Host

	$ sudo nano /etc/apache2/sites-available/catalog.conf

Add the following:
```
<VirtualHost *:80>
	ServerName domain or cloud server's IP
	ServerAdmin admin@yourwebsite.com or IP
	WSGIscriptAlias / /var/www/catalog/catalog.wsgi
	<Directory /var/www/catalog/catalog/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/catalog/catalog/static
	<Directory /var/www/catalog/catalog/static/>
		Order allow,deny
		Allow from all
	</Directory>
	Errorlog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable virtual host.

	$ sudo a2ensite catalog
	
Create .wsgi file in app directory.

	$ cd /var/www/catalog
	$ sudo nano catalog .wsgi
	
Add the following:
```
#!/usr/bin/python
import system
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")
from catalog import app as application
application.secret_key = 'your_secret_ key'
```
Restart Apache.

	$ sudo service apache2 restart
	
Enable all permissions to virtual environment.

	$ sudo chmod -R 777 catalogenv
	
Run the environment.
	
	$ source catalogenv /bin/activate
	
Install [http2](https://hyper.readthedocs.io/en/latest/quickstart.html#installing-hyper)

	$ pip install httplib2
	
Install [SQLAlchemy](http://flask-sqlalchemy.pocoo.org/2.1/)
		
	$ pip install sqlalchemy
	
Install [requests](http://docs.python-requests.org/en/master/)

	$ pip install requests
	
Install [werkzeug](http://werkzeug.pocoo.org/)(globally) and complete the setup for virtualenv
```	
$ pip install werkzeug=0.8.3 
$ pip install flask==0.90
$ pip install Flask-Login==0.1.3
$ pip install oath2client
$ sudo apt-get install python-psycopg2
```	
Change create engine command in your database setup.py file to:

> engine = create_engine('postgresql://user:password@localhost/catalog')

Run the database setup file.

	$ python bikesdatabase.py

To read apache errors run:

	$ sudo less /var/log/apache2/error.log
	
Clear the log

	$ sudo bash -c 'echo>/var/log/apache2/error.log'
	
If you have an error with `clients_secrets.json` file, give a full path to it in your python file.

Change string formatting of the URLs (write in one line) in you .py files if you see the formatting error.

## 6. Configure [Munin](http://munin-monitoring.org/) monitoring tool

Check your Apache version

	$ apache2 -–v

If it is not yet installed, you can go ahead and install it:

	$ sudo apt-get install apache2
	
Install Munin

	$ sudo apt-get install munin

Open configuration file

	sudo nano /etc/munin/munin.conf
	
Change
```
# dbdir /var/lib/munin
# htmldir /var/cache/munin/www
# logdir /var/log/munin
# rundir  /var/run/munin
```
to
```
dbdir /var/lib/munin
htmldir /var/www/munin
logdir /var/log/munin
rundir  /var/run/munin
```

Uncomment `tmpldir` line and localhost.localdomain should be updated to display the hostname, domain name
```
tmpldir /etc/munin/templates


[CatalogMuninMonitor]
    address 127.0.0.1
    use_node_name yes
```

Save the file and exit.

Open Munin Apache configuration.

	$ sudo nano /etc/munin/apache.conf

Change the beginning of this file to reflect this information:
```
Alias /munin /var/www/munin
<Directory /var/www/munin>
	Order allow,deny
	#Allow from localhost 127.0.0.0/8  ::1
	Allow from all
	Options None
```
Create the directory path that you referenced in the munin.conf file and modify the ownership to allow munin to write to it

	$ sudo mkdir /var/www/munin
	$ sudo chown munin:munin /var/www/munin
	
Restart apache and munin.

	$ sudo service munin-node restart
	$ sudo service apache2 restart
	
After about five minutes, your files should be created and you will be able to access your data. You should be able to access your munin details at:
```
your_ip_address/munin
```

### That's it you are ready to go! Feel free to make any changes to the provided code.


### CONTACT

Please send you feedback to

  max.savin3@gmail.com
