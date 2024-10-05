+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline Job - Deploy to EKS fetching image from AWS ECR.</br>

## Jenkins Pipeline Job - Deploy to EKS fetching image from AWS ECR.

### Prerequisites:
+ Jenkins is installed
+ Docker is installed
+ AWS cli is installed
+ AWS EKS is created
+ IAM user is created.  User name: dev
+ Github token generate
+ kubectl is installed

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration
 + Pipeline: AWS Steps

### Step 2: Create the AWS ECR  repository
```xml
Name: microservice-one
```
### Step 3: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]

```
### Step 4: Write the Kubernetes Deployment and Service manifest files.
##### deployment.yaml
```xml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice-one
  namespace: dev
spec:
  replicas: 2
  selector:
    matchLabels:
      app: microservice-one
  template:
    metadata:
      labels:
        app: microservice-one
    spec:
      containers:
      - name: microservice-one
        image: 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
##### service.yaml
```xml

apiVersion: v1
kind: Service
metadata:
  name: microservice-one
  namespace: dev
spec:
  selector:
    app: microservice-one
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 32000
  type: NodePort

```

### Step 5: Create the Jenkins Pipeline job
```xml
Job Name: deploy-to-eks-ecr-jenkins-pipeline
```
### Step 6: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-ecr-jenkinsfile
```

### Step 7: Write the Jenkinsfile
  + ### Step 7.1: Clone the repository 
```xml
stage('Clone the repository'){
        steps{
          git branch: 'deploy-to-eks-ecr-jenkinsfile', credentialsId: 'Github_credentails', url: 'https://github.com/techworldwithmurali/microservice-one.git'
          
        } 
      }
```
  + ### Step 7.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
```
  + ### Step 7.3: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
              docker build . --tag microservice-one:$BUILD_NUMBER
              docker tag microservice-one:$BUILD_NUMBER 108290765801.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
                
                '''
                
            }
        }
   
```
+ ### Step 7.4: Push Docker Image to AWS ECR

```xml
stage('Push Docker Image') {
 withAWS(credentials: 'AWS', region: 'us-east-1') {
       
                    sh '''
                   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 108290765801.dkr.ecr.us-east-1.amazonaws.com
                   docker push 108290765801.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
                    '''
                }
            } 
            
        }
```
+ ### Step 7.5:Deploy to AWS EKS
```xml
stage('Deployto AWS EKS') {
            steps {
                // configure AWS credentials
               withAWS(credentials: 'AWS', region: 'us-east-1') {

                   // Connect to the EKS cluster
                    sh '''
                     aws eks update-kubeconfig --name dev-cluster --region us-east-1'

                    // apply YAML files to EKS cluster
                      cd kubernetes-yaml
                      kubectl apply -f .
                      kubectl set image deployment/microservice-one microservice-one=108290765801.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
                    '''
                }
           
        }
            
        }
```
### Step 8:Verify whether pods are running or not
```xml
kubectl get pods -A
```
### Step 9: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one
```
Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Pipeline job.
