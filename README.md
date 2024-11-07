+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Build, push to Jfrog, and deploy the WAR file to Tomcat.</br>

## Jenkins Pipeline - Build, push to Jfrog, and deploy the WAR file to Tomcat

### Prerequisites:
  + Jenkins is installed
  + Jfrog artifactory is installed
  + Github token generate
  + Tomcat 9 Installed

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + artifactory
  + Deploy to container
  
### Step 2: Create the user in Jfrog
```xml
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the maven repository in Jfrog
```xml
Repository Name: tech-snapshots and tech-releases
```
### Step 4: Create the Jenkins Pipeline job
```xml
Job Name: build-and-push-to-jfrog-deploy-tomcat-pipeline
```
### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-nexus-deploy-tomcat-jenkinsfile
```
### Step 6: Write the Jenkinsfile
  + ### Step 6.1: Clone the repository 
```xml
stage('Clone the Repository ') {
            steps {
               git branch: 'build-and-push-to-nexus-deploy-tomcat-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
               
               
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

+ ### 6.4: Deploy the war file in Tomcat
```xml
stage('Deploy to tomcat') {
            steps {
            deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'https://tomcat.techworldwithmurali.in')], contextPath: null, war: '**/*.war'
                
            }
        }
  
```

### Step 7: Verify whether application is up and running or not.


#### Congratulations. You have successfully deployed the war file in Tomcat  using Jenkins Pipeline job.


