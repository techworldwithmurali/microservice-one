# micro-service-one

+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Dockerizing and Pushing to Jfrog repository.</br>

## Jenkins Pipeline - Dockerizing and Pushing to Jfrog repository.

### Prerequisites:
  + Jenkins is installed
  + Docker is installed
  + Jfrog is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  
### Step 2: Create the user in Jfrog
```xml
UserName: moole
Password: Techworld@2580
```
### Step 3: Create the docker repository in Jfrog
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
### Step 5: Create the Jenkins pipeline job
```xml
Job Name: pushing-docker-image-to-jfrog-jenkins-pipeline
```

### Step 6: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-jfrog-jenkinsfile
```


### Step 7: Write the Jenkinsfile
  + ### Step 7.1: Clone the repository 
```xml
stage('Clone the repository') {
            steps {
               git branch: 'pushing-docker-image-to-ecr-jenkinsfile', credentialsId: 'Github_credentails', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
```
  + ### Step 7.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
```
  + ### 7.3: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
              docker build . --tag microservice-one:latest
              docker tag microservice-one:latest a0alrhqhzjivs.jfrog.io/web-application/microservice-one:latest
                
                '''
                
            }
        }
   
```
+ ### 7.4: Push Docker Image to Jfrog artifactory
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'jfrog_crdenatils', passwordVariable: 'JFROG_PASSWORD', usernameVariable: 'JFROG_USERNAME')]) {
       
                    sh '''
                    docker login -u $JFROG_USERNAME -p $JFROG_PASSWORD a0alrhqhzjivs.jfrog.io
                        docker push a0alrhqhzjivs.jfrog.io/microservice-one/microservice-one:latest
                    '''
                }
            } 
            
        }
```


### Step 11: Verify whether docker image is pushed or not in Jfrog Artifactory

#### Congratulations. You have successfully pushed the docker image to Jfrog Artifactory through Jenkins Pipeline job.

