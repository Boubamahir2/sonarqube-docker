#Install SonarQube using Docker | Install SonarQube using Docker on Ubuntu 18.0.#4 | Install SonarQube using Docker-Compose
#How to setup Sonarqube using Docker and Docker compose?
#SonarQube is static code analyis tool. It can be installed quickly using Docker with less manual steps.

###Pre-requisites:
Ubuntu EC2 up and running with at least t2.small
Port 9000 is opened in security firewall rule
Install Docker
Run the following command to uninstall all conflicting packages:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

Install using the apt repository
Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.
Set up Docker's apt repository.
 <!-- Add Docker's official GPG key: -->
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

<!-- # Add the repository to Apt sources: -->
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

Install the Docker packages.

Latest Specific version
To install the latest version, run:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Verify that the Docker Engine installation is successful by running the hello-world image.

 sudo docker run hello-world

Install Docker-Compose
sudo apt-get install docker-compose -y

What is Docker Compose?
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.
 
The purpose of docker-compose is to function as docker cli but to issue multiple commands much more quickly. To make use of docker-compose, you need to encode the commands you were running before into a docker-compose.yml file
 
Run docker-compose up and Compose starts and runs your entire app.

Create docker-compose.yml
this yml has all configuration for installing both SonarQube and Postgresql:
sudo vi docker-compose.yml 

(Copy the below code high-lighted in yellow color)

(Copy the below code high-lighted in yellow color)
version: "3"

services:
  sonarqube:
    image: sonarqube:6.7.1
    container_name: sonarqube
    restart: unless-stopped
    environment:
      - SONARQUBE_JDBC_USERNAME=sonar
      - SONARQUBE_JDBC_PASSWORD=password123
      - SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonarqube
    ports:
      - "9000:9000"
      - "9092:9092"
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins

  db:
    image: postgres:10.1
    container_name: db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=password123
      - POSTGRES_DB=sonarqube
    volumes:
      - sonarqube_db:/var/lib/postgresql10
      - postgresql_data:/var/lib/postgresql10/data

volumes:
  postgresql_data:
  sonarqube_bundled-plugins:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_db:
  sonarqube_extensions:

Save the file by entering :wq!


Now execute the compose file using Docker compose command:
sudo docker-compose up -d 



If you are getting any errors like this, make sure you exit below commands to adding Docker group to current user.
"couldn't connect to docker daemon"

sudo usermod -aG docker $USER 

Make sure SonarQube is up and running
sudo docker-compose logs --follow
# sonarqube-docker
