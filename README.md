+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Dockerizing and Pushing to DockerHub.</br>

## Jenkins Pipeline - Dockerizing and Pushing to DockerHub.

### Prerequisites:
+ Jenkins is installed
+  Github token generate

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration
 + Docker

### Step 2: Create the Docker repository
```xml
Repository Name: microservice-one
```

### Step 3: Create the Jenkins Pipeline job
```xml
Job Name: pushing-docker-image-to-dockerhub-jenkins-pipeline
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-dockerhub-jenkinsfile
```


### Step 5: Write the Jenkinsfile
  + ### Step 5.1: Clone the repository 
```xml
stage('Clone') {
            steps {
                git branch: 'pushing-docker-image-to-dockerhub-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
```
  + ### Step 5.2: Build the code
```xml
stage('Build') {
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
               docker tag microservice-one:latest mmreddy424/microservice-one:latest
                
                '''
                
            }
        }
   
```
+ ###  6.2: Push Docker Image
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
       
                    sh '''
                    docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD
                        docker push mmreddy424/microservice-one:latest
                    '''
                }
            } 
            
        }
```


### Step 7: Verify whether docker image is pushed or not in DockerHub

##### Congratulations. You have successfully pushed the docker image to DockerHub using Jenkins Pipeline job.

