+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually building and pushing artifacts(war) to Jfrog Artifactory and deploy to tomcat.</br>

## Manually - Build and Push to Jfrog Artifactory and deploy to tomcat.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Jfrog Artifactory is installed
+ Create maven repository in Jfrog
+ Tomcat 9 is installed

### Step 1: Clone the repository
  ```xml
  git clone https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: build-and-push-to-jfrog-deploy-tomcat
```
### Step 2: Create the user in Jfrog
```xml
User Name: devops
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

### Step 5: Update the jfrog credentials in settings.xml
```xml
<servers>
    <server>
      <id>jfrog-snapshots</id>
      <username>devops</username>
      <password>Techworld@2580</password>
    </server>
    <server>
      <id>jfrog-releases</id>
      <username>devops</username>
      <password>Techworld@2580</password>
    </server>
  </servers>
```
### Step 6: Run the below command to push the artifacts to Jfrog Artifactory.
```sh
mvn deploy
```
### Step 7: Verify whether artifact(war) is published or not in Jfrog Artifactory.

### Step 8 : Deploy the war file in Tomcat webapps folder.
### Step 10 : Verify whether application is up and running or not

#### Congratulations. You have successfully deployed the artifact(war) file in Tomcat.
