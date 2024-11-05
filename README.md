+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually build and generate the static code analysis report using SonarQube.</br>

## Manually - Build and generate the static code analysis report using SonarQube.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ SonarQube is installed

### Step 1: Clone the repository
  ```xml
  git clone https://github.com/techworldwithmurali/microservice-one.git
 Branch Name: static-code-analysis
```
### Step 2: Create the SonarQube token
### Step 3: Update the SonarQube details in pom.xml
```xml
<dependency>
<groupId>org.sonarsource.scanner.maven</groupId>
<artifactId>sonar-maven-plugin</artifactId>
<version>3.2</version>
</dependency>

<profiles>
<profile>
<id>sonar</id>
<activation>
<activeByDefault>true</activeByDefault>
</activation>
<properties>
<!-- Optional URL to server. Default value is http://localhost:9000 -->
<sonar.host.url>
https://sonarqube.techworldwithmurali.in
</sonar.host.url>
</properties>
</profile>
</profiles>

```
### Step 4: Run below command to generate the static code analysis report.
```sh
mvn sonar:sonar -Dsonar.sonar.host.url=https://sonarqube.techworldwithmurali.in -Dsonar.login=a59971a4cf3ee650a17c928570ce7fb268c36a90
```
### Step 5: Verify whether SonarQube report is generated or not in SonarQube Dashboard.

#### Congratulations. You have successfully Published the static code analysis report in SonarQube..
