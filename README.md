+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually Dockerizing and Pushing to Jfrog Artifactory.</br>

## Manually - Dockerizing and Pushing to Jfrog Artifactory

### Prerequisites:
+ Git is installed
+ Java 17 installed
+ Maven is installed
+ Docker is installed
+ docker repository in Jfrog Artifactory

### Step 1: Clone the repository
  
```xml
 github url: https://github.com/techworldwithmurali/microservice-one.git
 branch name: pushing-docker-image-to-jfrog
```
### Step 2: build the code
```xml
mvn package
```
### Step 3:
Step 3.1: Create the user in Jfrog
```xml
UserName: devops
Password: Techworld@2580
```
Step 3.2: Create the docker repository in Jfrog Artifactory
```xml
Repository Name: tech
```
### Step 4: Write the Dockerfile
```xml
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```

### Step 5: Build the Docker image
```xml
docker build . --tag microservice-one:latest
```
### Step 6: Login to Jfrog Artifactory in local
```xml
docker login -umoole jfrog.techworldwithmurali.in
```
### Step 7: tag and push the docker image to Jfrog Artifactory
```xml
docker tag microservice-one:latest jfrog.techworldwithmurali.in/tech/microservice-one:latest

docker push jfrog.techworldwithmurali.in/tech/microservice-one:latest
```

### Step 8: Verify whether docker image is pushed or not in Jfrog Artifactory.


#### Congratulations. You have successfully Pushed the docker image to Jfrog Artifactory.
