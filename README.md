+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle - building and pushing artifacts(war) to Jfrog Artifactory</br>

## Jenkins Freestyle - Build and Push to Jfrog Artifactory

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
Repository Name: tech-snapshots and tech-releases
```
### Step 4: Update the jfrog Artifactory details in pom.xml
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
### Step 5: Create the Jenkins Freestyle job
```xml
Job Name: build-and-push-to-jfrog
```
### Step 6: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-jfrog-freestyle
```
### Step 7: Configure the Invoke Artifactory Maven 3
      clean install
### Step 8: Configure the Maven3 artifactory Integration

### Step 9: Verify whether artifact(war) is published or not in Jfrog Artifactory.

#### Congratulations. You have successfully published the artifact(war) file in Jfrog repository using Jenkins Freestyle job.

