+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline Job - Dockerizing and Pushing to AWS ECR.</br>

## Jenkins Pipeline Job - Dockerizing and Pushing to AWS ECR..

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

### Step 3: Create the Jenkins Pipeline job
```xml
Job Name: pushing-docker-image-to-ecr-jenkins-pipeline
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-ecr-jenkinsfile
```

### Step 5: Write the Jenkinsfile
  + ### Step 5.1: Clone the repository 
```xml
stage('Clone the repository') {
            steps {
               git branch: 'pushing-docker-image-to-ecr-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
```
  + ### Step 5.2: Build the code
```xml
stage('Build the application') {
            steps {
                sh 'mvn clean install'
            }
        }
```
### Step 6: Write the Dockerfile
```xml
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]

```
  + ### 6.1: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
              docker build . --tag microservice-one:latest
              docker tag microservice-one:latest 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
                
                '''
                
            }
        }
   
```
+ ### 6.2: Push Docker Image to AWS ECR

```xml
stage('Push Docker Image to AWS ECR') {
steps{
                    sh '''
                   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267221649.dkr.ecr.us-east-1.amazonaws.com
                   docker push 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
                    '''
            } 

        }
```


### Step 7: Verify whether docker image is pushed or not in AWS ECR

#### Congratulations. You have successfully Pushed the docker image to AWS ECR using Jenkins Pipeline job.
