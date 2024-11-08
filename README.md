+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Build and Push to Jfrog artifactory</br>

## Jenkins Pipeline - Build and Push to Jfrog artifactory

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
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the maven repository in Jfrog
```xml
Repository Name: tech-snapshots and tech-releases
```
### Step 3.1: Update the Jfrog Artifactory details in pom.xml
```xml
  <distributionManagement>
      <snapshotRepository>
        <id>jfrog-snapshots</id>
        <url>https://jfrog.techworldwithmurali.in/artifactory/tech-snapshots/</url>
      </snapshotRepository>
      <repository>
        <id>jfrog-releases</id>
        <url>https://jfrog.techworldwithmurali.in/artifactory/tech-releases/</url>
      </repository>
    </distributionManagement>
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
stage('Push the artifacts into Jfrog Artifactory') {
    steps {
        script {
            // Get the current date and time in the format: yyyy-MM-dd_HH-mm
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Define the target path with the timestamp
            def targetPath = "microservice-one/${currentDate}/"

            // Upload the artifact to JFrog Artifactory with the timestamped path
            rtUpload(
                serverId: 'jfrog',
                spec: """
                    {
                        "files": [
                            {
                                "pattern": "*.war",
                                "target": "${targetPath}"
                            }
                        ]
                    }
                """
            )
        }
    }
}
  
```

### Step 7: Verify whether the WAR file was pushed to JFrog or not


#### Congratulations. Congratulations! You have successfully pushed the WAR file to JFrog using the Jenkins pipeline job.


