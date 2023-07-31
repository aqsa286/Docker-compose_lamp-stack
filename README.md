# Create a lamp stack using docker compose 

### Step-1: Installing packages and starting the docker service.
        
First thing we have to do is to install the software we need: 
Docker itself, and Docker-compose, which is an utility that let us easily organize multi-container applications using yaml configuration files. 

Install docker-compose package:
   
    sudo apt install  docker-compose
Start Service:
    
    sudo systemctl enable --now docker
    
### Step-2: Project setup.

 First step we will create directory that will used as a root directory.  we will call it linuxconfig. Inside this directory we will create another one, DocumentRoot, which will host our website files. 

Create directory:

    mkdir -p linuxconfig/DocumentRoot
      
:: Linuxconfig  → Inside this directory we will define docker-compose configuration file ‘docker-compose.yml’. We can use in the configuration file:
* Services
** Volumes
### Networks

#### Services
  A service definition contains the configuration that is applied to each container started for that service. Each service may also include a Build section, which defines how to create the Docker image for the service.

#### Volumes 
  Docker volumes are dependent on Docker’s file system and are the preferred method of persisting data for Docker containers and services. When a container is started, Docker loads the read-only image layer, adds a read-write layer on top of the image stack, and mounts volumes onto the container filesystem.

#### Networks
  Docker includes a networking system for managing communications between containers, your Docker host, and the outside world. Several different network types are supported, facilitating a variety of common use cases.
Step-3: Defining the php + httpd service.
    
 The first service we will define in the configuration file will include PHP as the Apache web server module. 
 Let’s start writing our configuration:
Change directory
        
    cd linuxconfig 
Create and edit file
   
    nano docker-compose.yml

    version: '3.7'

    services:
        php-httpd:
            image: php:7.3-apache
            ports:
              - 80:80
            volumes:
              - "./DocumentRoot:/var/www/html"


The first thing we specified in the configuration file is version. version 3.7 is the latest .


Step-4: 
          After declaring the compose file version, we started writing the service; inside of it we define the services which will compose our LAMP stack. We called the first service php-httpd. The service name is completely arbitrary, but is always a good habit to use one that is meaningful in the context of the project.
The image instruction is used to specify on which image the container should be based, in this case php:7.3-apache.

    echo "<?php phpinfo();" > DocumentRoot/index.php
    
    sudo docker-compose up -d

 After executing the command, the needed docker image will be downloaded from dockerhub and the containers we will be created with the settings we provided and run in the background(they will not block terminal), because of using -d option we provided to the docker-compose command. 
Now after project up and running, if we navigate to localhost or ip on browser. 
We will see php page.


For stop project:

    sudo docker-compose stop
Defining the MariaDB service:
                  An essential part of the LAMP stack is the database layer. In our configuration we will use MariaDB and its official docker image available on dockerhub

    nano docker-compose.yml

    version: '3.7'

    services:
        php-httpd:
            image: php:7.3-apache
             ports:
               - 80:80
             volumes:
               - "./DocumentRoot:/var/www/html"

        mariadb:
            image: mariadb:10.5.2
            volumes:
               - mariadb-volume:/var/lib/mysql
            environment:
               TZ: "Europe/Rome"
               MYSQL_ALLOW_EMPTY_PASSWORD: "no"
               MYSQL_ROOT_PASSWORD: "rootpwd"
               MYSQL_USER: 'testuser'
               MYSQL_PASSWORD: 'testpassword'
               MYSQL_DATABASE: 'testdb'

        volumes:
          mariadb-volume:


Bonus – phpMyAdmin:
    Our basic LAMP stack should now be complete. As a bonus, we may want to add phpMyAdmin to it, in order to easily control our MariaDB database from a user-friendly web interface.
Let’s add the related service definition to our docker-compose configuration:

    version: '3.7'

    services:
       php-httpd:
           image: php:7.3-apache
           ports:
              - 80:80
           volumes:
              - "./DocumentRoot:/var/www/html"

       mariadb:
           image: mariadb:10.5.2
           volumes:
              - mariadb-volume:/var/lib/mysql
           environment:
              TZ: "Europe/Rome"
              MYSQL_ALLOW_EMPTY_PASSWORD: "no"
              MYSQL_ROOT_PASSWORD: "rootpwd"
              MYSQL_USER: 'testuser'
              MYSQL_PASSWORD: 'testpassword'
              MYSQL_DATABASE: 'testdb'

       phpmyadmin:
           image: phpmyadmin/phpmyadmin
           links:
              - 'mariadb:db'
           ports:
              - 8081:80
 
     volumes:
         mariadb-volume:

The docker compose up command aggregates the output of each container

    sudo docker-compose up -d --build

The phpMyAdmin interface will be therefore reachable at the localhost:8081 address. 

The PhpMyAdmin login page will appear here.

We can login with the credentials we defined for our database service, and verify that the testdb database has been created: 

Now PhpMyAdmin homepage will appear here.

Using a custom image for a service:
   For example, say we want to build the php-httpd service, but include an additional php extension: how can we do it? 
On the root of the project, we define a new directory, and for convenience name it after the service:
 Create new directory

    mkdir php-httpd
Inside this directory we create a Dockerfile.

Change directory

    cd php-httpd
Create file

    nano Dockerfile
Dockerfile:

    FROM php:7.3-apache
    LABEL maintainer="egdoc.dev@gmail.com"

    RUN apt-get update && apt-get install -y libmcrypt-dev \
        && pecl install mcrypt-1.0.2 \
        && docker-php-ext-enable mcrypt


Back in our docker-compose.yml file, we modify the definition of the php-httpd service. We cannot reference the image directly as we did before. Instead, we specify the directory containing our custom Dockerfile as the build context:

    nano docker-compose.yml

Docker compose configuration file.

    version: '3.7'

    services:
       php-httpd:
           build:
               context: ./php-httpd
           ports:
              - 80:80
           volumes:
               - "./DocumentRoot:/var/www/html"
      [...]



In the build section we define configurations that are applied at build time.

