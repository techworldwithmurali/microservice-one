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
Name: user-registration
```
### Step 3:  Create the Build Jenkins job under user-registration folder
```xml
Job Name: build
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/user-registration.git
Branch : deploy-to-eks-ecr-jenkinsfile
```
### Step 5: Write the Jenkinsfile
  + ### Step 5.1: Clone the repository 
```xml
stage('Clone the repository'){
        steps{
          git branch: 'deploy-to-eks-ecr-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/user-registration.git'
          
        } 
      }
```
  + ### Step 5.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
```
### Step 6: Write the Dockerfile
```xml
# Use an OpenJDK base image
FROM openjdk:17-jdk-slim

# Set the working directory in the container
WORKDIR /app

# Copy the JAR file from the target directory
COPY target/*.jar /app/user-registration.jar

# Expose the application's port (update if your application uses a specific port)
EXPOSE 80

# Command to run the application
CMD ["java", "-jar", "/app/user-registration.jar"]

```
  + ### 6.1: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
               IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
               docker build . --tag user-registration:$IMAGE_TAG
               docker tag user-registration:$IMAGE_TAG mmreddy424/user-registration:$IMAGE_TAG
                
                '''
                
            }
        }
   
```
+ ### 6.2 Push Docker Image
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
       
                    sh '''
                   IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                    docker push mmreddy424/user-registration:$IMAGE_TAG
                    '''
                }
            } 
            
        }
```
### Step 7: Verify whether docker image is pushed or not in DockerHub

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Jenkins Job - dev-deploy
### Step 1: Attach the IAM role to the Jenkins server
### Step 2: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/user-registration.git
Branch : deploy-to-eks-ecr-jenkinsfile
```
### Step 3: Write the Kubernetes Deployment and Service manifest files.
##### deployment.yaml
```xml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-registration
  namespace: sample-ns
spec:
  replicas: 2
  selector:
    matchLabels:
      app: user-registration
  template:
    metadata:
      labels:
        app: user-registration
    spec:
      containers:
      - name: user-registration
        image: 266735810449.dkr.ecr.us-east-1.amazonaws.com/user-registration:latest
```
##### service.yaml
```xml

apiVersion: v1
kind: Service
metadata:
  name: user-registration
  namespace: sample-ns
spec:
  selector:
    app: user-registration
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: NodePort

```
### Step 4: Write the Jenkinsfile
  + ### Step 4.1: Clone the repository 
```xml
stage('Clone') {
            steps {
                git branch: 'deploy-to-eks-ecr-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/user-registration.git'
            }
        }
```

+ ### Step 4.2: Set Up AWS EKS Config
```xml
stage('Set Up AWS EKS Config') {
            steps {
                script {
                    sh """
                    aws eks update-kubeconfig --name ${EKS_CLUSTER} --region ${AWS_REGION}
                    aws sts get-caller-identity
                    """
                }
            }
        }
```
+ ### Step 4.3: Update Deployment File
```xml
stage('Update Deployment File') {
            steps {
                script {
                    sh """
                    sed -i 's|__TAG__|${params.IMAGE_TAG}|g' ${DEPLOYMENT_FILE}
                    cat ${DEPLOYMENT_FILE}
                    """
                }
            }
        }
```
+ ### Step 4.4: Apply Kubernetes Manifests
```xml
stage('Apply Kubernetes Manifests') {
            steps {
                script {
                    sh """
                    kubectl apply -f ${DEPLOYMENT_FILE}
                    kubectl apply -f .
                    """
                }
            }
        }
```
### Step 6: Access java application through NodePort.
```xml
http://node-IP:port
```
### Step 7: Deploy Ingress Resource for This Application
```xml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: user-management
  namespace: user-management
  annotations:
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/tags: app=techworldwithmurali,Team=DevOps
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:266735810449:certificate/8a7cbcb1-774c-463f-ab3e-476437028686
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    alb.ingress.kubernetes.io/security-groups: sg-026c5ab74985fa179

spec:
  ingressClassName: alb
  rules:
    - host: user-registration.techworldwithmurali.in
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: user-registration
                port:
                  number: 80

```

### Step 8: Check Whether Load Balancer, Rules, and DNS Records Are Created in Route 53

### Step 9: Access java application through DNS record Name.
```
https://myapp-dev.techworldwithmurali.in/user-registration/
```

### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Pipeline job.

