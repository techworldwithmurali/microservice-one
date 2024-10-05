+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - build and deploy the war file in Tomcat</br>

## Jenkins Pipeline - build and deploy the war file in Tomcat

### Prerequisites:
  + Jenkins is installed
  + Tomcat 7 is  installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + Deploy to container
  
### Step 2: Create the Jenkins Pipeline job
```xml
Job Name: build-and-deploy-to-tomcat-jenkins-pipeline
```
### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-deploy-to-tomcat-jenkinsfile
```
### Step 6: Write the Jenkinsfile
  + ### Step 6.1: Clone the repository 
```xml
stage('Clone the repository') {
            steps {
               git branch: 'build-and-deploy-to-tomcat-jenkinsfile', credentialsId: 'Github_credentails', url: 'https://github.com/techworldwithmurali/microservice-one.git'
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
  + ### 6.3: Deploy the war file in Tomcat
```xml
stage('Deploy to tomcat') {
            steps {
            deploy adapters: [tomcat7(credentialsId: 'Tomcat-7-cred', path: '', url: 'http://100.25.166.179:8080')], contextPath: null, war: '**/*.war'
                
            }
        }
  
```

### Step 8: Verify whether application is up and running or not.

#### Congratulations. ou have successfully deployed the artifact(war) file in Tomcat using Jenkins Pipeline job.
