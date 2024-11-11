+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle Job - Dockerizing and Pushing to AWS ECR.</br>

## Jenkins Freestyle Job - Dockerizing and Pushing to AWS ECR..

### Prerequisites:
+ Jenkins is installed
+ Docker is installed
+ AWS cli is installed
+ Github token generate

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration

### Step 2: Create the AWS ECR repository
```xml
Name: microservice-one
```

### Step 3: Create the Jenkins Freestyle job
```xml
Job Name: pushing-docker-image-to-ecr
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-ecr-freestyle
```
### Step 5: Invoke the top level maven targets
```xml
clean package
```
### Step 6: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 7: Build and tag the Docker image
```xml
docker build . --tag microservice-one:latest
docker tag microservice-one:latest 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
### Step 11: Configure the AWS credenatils in Jenkins Server or attach the IAM role to the jenkins server 
```xml
aws configure
```
### Step 8: login to AWS ECR
```xml
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267221649.dkr.ecr.us-east-1.amazonaws.com
```
### Step 9: Push to AWS ECR
```xml
docker push 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
### Step 10: Verify whether docker image is pushed or not in AWS ECR

#### Congratulations. You have successfully Pushed the docker image to AWS ECR using Jenkins freestyle job.
