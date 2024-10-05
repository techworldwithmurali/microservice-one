+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b>Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually Dockerizing and Pushing to AWS ECR.</br>

## Manually - Dockerizing and Pushing to AWS ECR

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Docker is installed
+ AWS ECR repository is created

### Step 1: Clone the repository
  
```xml
  github url: https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: pushing-docker-image-to-ecr
```
### Step 2: build the code
```xml
mvn package
```
### Step 3: Create the repository in AWS ECR
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

docker tag microservice-one:latest 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
### Step 6: Login to AWS ECR in local
```xml
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267221649.dkr.ecr.us-east-1.amazonaws.com
```

### Step 7: Push the docker image to DockerHub
```xml
docker push 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
### Step 8: Verify whether docker image is pushed or not in AWS ECR


#### Congratulations. You have successfully Pushed the docker image to AWS ECR.
