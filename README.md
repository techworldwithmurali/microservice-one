+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle - Static Code Analysis using SonarQube</br>

## Jenkins Freestyle - Static Code Analysis using SonarQube

### Prerequisites:
  + Jenkins is installed
  + SonarQube is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  + SonarQube Scanner plugin
  
### Step 2: Create the Jenkins Freestyle job
```xml
Job Name: static-code-analysis
```
### Step 3: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : static-code-analysis-freestyle
```
### Step 4: Configure the Execute SonarQube Scanner
##### soanrqube.properties
```xml
sonar.projectKey=microservice-one
sonar.projectName=microservice-one
sonar.projectVersion=1.0-SNAPSHOT
sonar.issuesReport.html.enable=true
sonar.sources=.
sonar.java.binaries=target/classes

sonar.tests=src/test/java
sonar.java.test.binaries=target/test-classes
sonar.junit.reportPaths=target/surefire-reports
```

### Step 5: Verify whether SonarQube report is generated or not in SonarQube Dashboard.

#### Congratulations. You have successfully Published the static code analysis report in SonarQube using Jenkins Freestyle job.
