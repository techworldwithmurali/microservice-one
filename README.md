+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for building and generating the WAR file using Jenkins Freestyle jobs </br>

## Jenkins Freestyle - Build and Push to Jfrog Artifactory

### Prerequisites:
  + Jenkins is installed
  + Github token generate

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  

### Step 2: Create the Jenkins Freestyle job
```xml
Job Name: build-freestyle
```
### Step 3: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : build-and-push-to-jfrog-freestyle
```
### Step 4: Configure the Invoke top-level maven targets
      clean install
### Step 5: Verify whether artifact(war) is generated or not

#### Congratulations. You have successfullygenerated the war file using Jenkins Freestyle job.

