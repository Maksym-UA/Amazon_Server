# Linux server configuration

## Bike catalog app

This project teaches you to secure and set up a Linux server. 
The project is running on [Amazon Lightsail](https://portal.aws.amazon.com/billing/signup). 
Currently the app is running on [http://35.159.1.90/](http://35.159.1.90/).


## SETUP

### Amazon Lightsail instance
First, log in to Lightsail. If you don't already have an [Amazon Web Services]((https://portal.aws.amazon.com/billing/signup). ) 
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
The public IP address of the instance is displayed along with its name. In the above picture it's 35.159.1.90. 
Explore the other tabs of the user interface to find the Lightsail firewall and other settings. 

When you SSH in, you'll be logged as the ubuntu user. When you want to execute commands as `root`, you'll need to use the `sudo` command to do it.

### That's it you are ready to go! Feel free to make any changes to the provided code.


### CONTACT

Please send you feedback to

  max.savin3@gmail.com
