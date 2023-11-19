# Jumstart Guacamole with docker-compose
This is a small documentation how to run a fully working **Apache Guacamole (incubating)** instance with docker (docker-compose). The goal of this project is to make it easy to start Guacamole.


## About Guacamole
Apache Guacamole (incubating) is a clientless remote desktop gateway. It supports standard protocols like VNC, RDP, and SSH. It is called clientless because no plugins or client software are required. Thanks to HTML5, once Guacamole is installed on a server, all you need to access your desktops is a web browser.

It supports RDP, SSH, Telnet and VNC and is the fastest HTML5 gateway I know. Checkout the projects [homepage](https://guacamole.incubator.apache.org/) for more information.

## Prerequisites
You need a working **docker** installation and **docker-compose** running on your machine.

## Quick start
Clone the GIT repository and start guacamole:

~~~bash
git clone "https://github.com/syn4ps/guacamole-docker-compose.git"
cd guacamole-docker-compose
docker-compose up -d
~~~

Web interface of your guacamole server should now be available at `http://ip_of_your_docker_server:8080/guacamole`. The default username is `guacadmin` with password `guacadmin`.

## Details
Compose file is based on this repo https://github.com/boschkundendienst/guacamole-docker-compose, but i reworked it for my convenience. May be it will be useful for anybody else or not :).
Database settings was moved to db.env file. I removed nginx service, because personally i prefer to use nginx-proxy-manager as frontend, it saves me from headaches with ssl certificate updates and is easy to set up.

Some details about services in `docker-compose.yml` and `db.env` files:

### db.env
File with enviroment variables for database, such as database name, username , host and password. At first run init service will take variables from this file and create this db.
Don't forget to change password before running docker-compose up -d  

### Services

#### guacd
The following part of docker-compose.yml will create the guacd service. guacd is the heart of Guacamole which dynamically loads support for remote desktop protocols (called "client plugins") and connects them to remote desktops based on instructions received from the web application. 

#### postgres
The following part of docker-compose.yml will create an instance of PostgreSQL using the 16.1 official docker image based on Alpine linux. All data located in guacamole_pgdata presistent volume.  

#### init
This service based on PostgreSQL image too, that is running for first time database initialization. I moved all init from .sh files into this service. If you will re-run docker-compose it checks for existing of database from db.env file and if database already exist it skipping init (your data will be in safe). It run , init database and exit, you can check this service log for details.   

#### guacamole
The following part of docker-compose.yml will create an instance of guacamole by using the docker image `guacamole` from docker hub. This service is web application of guacamole. In this setup it is configured to connect to the previously created postgres instance using a username and password and the database from db.env file. Port 8080 is published.
