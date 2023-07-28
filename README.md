# Create a lamp stack using docker compose 

### Step-1: Installing packages and starting the docker service.
        
First thing we have to do is to install the software we need: 
Docker itself, and Docker-compose, which is an utility that let us easily organize multi-container applications using yaml configuration files. 

####Install docker-compose package:
   
    sudo apt install  docker-compose
Start Service:
    
    sudo systemctl enable --now docker
    
Step-2: Project setup.
 
              First step we will create directory that will used as a root directory.  we will call it linuxconfig. Inside this directory we will create another one, DocumentRoot, which will host our website files. 

Create directory:
                   Cmd: mkdir -p linuxconfig/DocumentRoot
      
:: Linuxconfig  → Inside this directory we will define docker-compose configuration file ‘docker-compose.yml’. We can use in the configuration file:
Services
Volumes
Networks

