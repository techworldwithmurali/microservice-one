+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle - building and pushing artifacts(war) to Nexus Artifactory</br>

## Jenkins Freestyle - Build and Push to Nexus Artifactory

### Prerequisites:
  + Jenkins is installed
  + Nexus artifactory is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + Nexus artifact uploader
  
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
        <id>Nexus-snapshots</id>
        <url>https://Nexus.techworldwithmurali.in/artifactory/tech-snapshots/</url>
      </snapshotRepository>
      <repository>
        <id>Nexus-releases</id>
        <url>https://Nexus.techworldwithmurali.in/artifactory/tech-releases/</url>
      </repository>
    </distributionManagement>
```
### Step 5: Create the Jenkins Freestyle job
```xml
Job Name: build-and-push-to-Nexus
```
### Step 6: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-Nexus-freestyle
```
### Step 7: Configure the Invoke top level maven targets
      clean install
### Step 8: Configure the nexus artifact uploader

### Step 9: Verify whether artifact(war) is published or not in Nexus Artifactory.

#### Congratulations. You have successfully published the artifact(war) file in Nexus repository using Jenkins Freestyle job.

