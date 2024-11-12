+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins freestyle - Dockerizing and Pushing to Jfrog repository.</br>

## Jenkins freestyle - Dockerizing and Pushing to Jfrog repository.

### Prerequisites:
  + Jenkins is installed
  + Docker is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  
### Step 2: Create the user in Jfrog
```xml
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the docker repository in Jfrog
```xml
Repository Name: tech
```
### Step 4: Create the Jenkins Freestyle job
```xml
Job Name: pushing-docker-image-to-jfrog-freestyle
```

### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-jfrog-freestyle
```

### Step 6: Invoke the top level maven targets
```xml
clean package
```
### Step 7: Write the Dockerfile
```xml
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 8: Build the Docker image
```xml
docker build . --tag microservice-one:latest
```
### Step 9: Login to Jfrog in local
```xml
docker login -u devops -p Techworld@2580 jfrog.techworldwithmurali.in
```
### Step 10: tag and push to Jfrog artifactory
```xml
docker tag microservice-one:latest jfrog.techworldwithmurali.in/tech/microservice-one:latest

docker push jfrog.techworldwithmurali.in/tech/microservice-one:latest
```
### Step 11: Verify whether docker image is pushed or not in Jfrog Artifactory

#### Congratulations. You have successfully pushed the docker image to Jfrog Artifactory through Jenkins Freestyle job.
