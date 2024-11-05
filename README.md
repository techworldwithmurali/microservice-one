+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually building and pushing artifacts(war) to Nexus Artifactory and deploy to tomcat.</br>

## Manually - Build and Push to Nexus  Artifactory and deploy to tomcat.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Java 17 is Installed
+ Nexus Artifactory is installed
+ Create maven repository in Nexus
+ Tomcat 9 is installed

### Step 1: Clone the repository
  ```xml
  git clone https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: build-and-push-to-nexus-deploy-tomcat
```
### Step 2: Create the user in Nexus
```xml
User Name: moole
```
### Step 3: Create the maven repository in Nexus
```xml
Repository Name: tech-snapshots
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
### Step 5: Update the Nexus credentials in settings.xml
```xml
<servers>
    <server>
      <id>nexus-snapshots</id>
      <username>moole</username>
      <password>Techworld@2580</password>
    </server>
    <server>
      <id>nexus-releases</id>
      <username>moole</username>
      <password>Techworld@2580</password>
    </server>
  </servers>
```
### Step 6: Run the below command to push the artifacts to nexus Artifactory.
```sh
mvn deploy
```
### Step 7: Verify whether artifact(war) is published or not in nexus Artifactory.

### Step 8 : Deploy the war file in Tomcat webapps folder.
### Step 10 : Verify whether application is up and running or not

#### Congratulations. You have successfully deployed the artifact(war) file in Tomcat.
