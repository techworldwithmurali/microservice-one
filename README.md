+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - building and pushing artifacts(war) to Jfrog Artifactory</br>

## Jenkins Pipeline - Build and Push to Jfrog Artifactory

### Prerequisites:
  + Jenkins is installed
  + Jfrog artifactory is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + artifactory
  
### Step 2: Create the user in Jfrog
```xml
UserName: moole
Password: Techworld@2580
```
### Step 3: Create the maven repository in Jfrog
```xml
Repository Name: microservices
```
### Step 4: Create the Jenkins Pipeline job
```xml
Job Name: build-and-push-to-jfrog
```
### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-jfrog-jenkinsfile
```
### Step 6: Write the Jenkinsfile
  + ### Step 6.1: Clone the repository 
```xml
stage('Clone the Repository ') {
            steps {
               git branch: 'build-and-push-to-jfrog-jenkinsfile', credentialsId: 'Github_credentails', url: 'https://github.com/techworldwithmurali/microservice-one.git'
               
               
            }
        }
```
  + ### Step 6.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
```
  + ### 6.3: Push the artifacts to jfrog repository
```xml
 stage('Push the artifacts into Jfrog artifactory') {
            steps {
              rtUpload (
                serverId: 'Jfrog-dev-server',
                spec: '''{
                      "files": [
                        {
                          "pattern": "*.war",
                           "target": "microservice-one/"
                        }
                    ]
                }'''
              )
          }
        }
  
```

### Step 8: Verify whether artifact(war) is published or not in Jfrog Artifactory.

#### Congratulations. You have successfully published the artifact(war) file in Jfrog repository using Jenkins Pipeline job.

