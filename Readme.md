#Install SonarQube using Docker | Install SonarQube using Docker on Ubuntu 18.0.#4 | Install SonarQube using Docker-Compose
#How to setup Sonarqube using Docker and Docker compose?
#SonarQube is static code analyis tool. It can be installed quickly using Docker with less manual steps.

###Pre-requisites:
Ubuntu EC2 up and running with at least t2.small
Port 9000 is opened in security firewall rule
Install Docker
sudo apt-get install docker -y

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
