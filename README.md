+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle: Build, push to Nexus, and deploy the WAR file to Tomcat..</br>

## Jenkins Freestyle: Build, push to Nexus, and deploy the WAR file to Tomcat.

### Prerequisites:
  + Jenkins is installed
  + Tomcat 9 is installed
  + Nexus artifactory is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + Nexus artifact uploader
  + Deploy to container 
  
### Step 2: Create the user in Nexus
```xml
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the maven repository in Nexus
```xml
Repository Name: tech-snapshots and tech-releases
```
### Step 4: Update the Nexus Artifactory details in pom.xml
```xml
 <distributionManagement>
      <snapshotRepository>
        <id>nexus-snapshots</id>
        <url>https://nexus.techworldwithmurali.in/repository/tech-snapshots/</url>
      </snapshotRepository>
      <repository>
        <id>nexus-releases</id>
        <url>https://nexus.techworldwithmurali.in/repository/tech-releases/</url>
      </repository>
    </distributionManagement>
```
### Step 5: Create the Jenkins Freestyle job
```xml
Job Name: build-and-push-to-nexus-deploy-tomcat-freestyle
```
### Step 6: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-nexus-deploy-tomcat-freestyle
```
### Step 7: Configure the Invoke top level maven targets
      clean install
### Step 8: Configure the nexus artifact uploader

### Step 9: Verify whether artifact(war) is published or not in Nexus Artifactory.
### Step 10: Configure the tomcat under post build section
### Step 11: Verify whether application is deployed or not properly in tomcat

#### Congratulations. You have successfully deployed the artifact(war) file in Tomcat using Jenkins Freestyle job.
