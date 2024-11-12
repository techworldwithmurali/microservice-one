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
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the docker repository in Jfrog
```xml
Repository Name: tech
```

### Step 4: Create the Jenkins pipeline job
```xml
Job Name: pushing-docker-image-to-jfrog-jenkins-pipeline
```

### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : pushing-docker-image-to-jfrog-jenkinsfile
```


### Step 6: Write the Jenkinsfile
   #### Step 6.1: Clone the repository 
```xml
stage('Clone the repository') {
            steps {
               git branch: 'pushing-docker-image-to-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
```
   #### Step 6.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
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
   #### 7.1: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
              docker build . --tag microservice-one:latest
          docker tag microservice-one:latest jfrog.techworldwithmurali.in/tech/microservice-one:latest
                
                '''
                
            }
        }
   
```
 #### 7.2: Push Docker Image to Jfrog artifactory
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'jfrog-cred', passwordVariable: 'JFROG_PASSWORD', usernameVariable: 'JFROG_USER_NAME')]) {
       
                    sh '''
                   docker login -u devops -p Techworld@2580 jfrog.techworldwithmurali.in
                        docker push jfrog.techworldwithmurali.in/tech/microservice-one:latest
                    '''
                }
            } 
            
        }
```


### Step 11: Verify whether docker image is pushed or not in Jfrog Artifactory

#### Congratulations. You have successfully pushed the docker image to Jfrog Artifactory through Jenkins Pipeline job.

