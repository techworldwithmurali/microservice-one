+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b>Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually Dockerizing and Pushing to DockerHub.</br>

## Manually - Dockerizing and Pushing to DockerHub

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Docker is installed
+ DockerHub repository is created


### Step 1: Clone the repository
  
```xml
  github url: https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: pushing-docker-image-to-dockerhub
```
### Step 2: build the code
```xml
mvn package
```
### Step 3: Create the repository in DockerHub
```xml
Repository Name: microservice-one
```
### Step 4: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 5: Build and tag the Docker image
```xml
docker build . --tag microservice-one:latest
docker tag microservice-one:latest mmreddy424/microservice-one:latest
```
### Step 6: Login to DockerHub in local
```xml
docker login -u your-username -p your-password
```
### Step 7: Push the docker image to DockerHub
```xml
docker push mmreddy424/microservice-one:latest
```
### Step 8: Verify whether docker image is pushed or not in DockerHub

#### Congratulations. You have successfully pushed the docker image to DockerHub.
