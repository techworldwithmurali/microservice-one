+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins pipeline - Deploy to EKS fetching image from Jfrog repository.</br>

## Jenkins pipeline - Deploy to EKS fetching image from Jfrog repository.

### Prerequisites:
+  Jenkins is installed
+  Docker is installed
+  Github token generate
+  AWS CLI is installed
+  AWS EKS Cluster is created
+  AWS IAM User user created
+  kubectl is installed

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  
### Step 2: Create the user in Jfrog
```xml
UserName: devops
Password: Techworld@2580
```
### Step 3: Create the docker repository in Jfrog
```xml
Repository Name: tech
```
### Step 4:  Create the Build Jenkins job under microservice-one folder
```xml
Job Name: build
```
### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-jfrog-jenkinsfile
```
### Step 6: Write the Jenkinsfile
  + ### Step 6.1: Clone the repository 
```xml
stage('Clone the repository'){
        steps{
          git branch: 'deploy-to-eks-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
          
        } 
      }
```
  + ### Step 6.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
```
### Step 7: Write the Dockerfile
```xml
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
  + ### 7.1: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
               IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
               docker build . --tag microservice-one:$IMAGE_TAG
               docker tag microservice-one:$IMAGE_TAG mmreddy424/microservice-one:$IMAGE_TAG
                
                '''
                
            }
        }
   
```
+ ### 7.2 Push Docker Image
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'dockerhub-crde', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
       
                    sh '''
                   IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                    docker push mmreddy424/microservice-one:$IMAGE_TAG
                    '''
                }
            } 
            
        }
```
### Step 8: Verify whether docker image is pushed or not in Jfrog

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Jenkins Job - dev-deploy
### Step 1: Attach the IAM role to the Jenkins server
### Step 2: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-dockerhub-jenkinsfile
```

### Step 3: Write the Kubernetes Deployment and Service manifest files.
##### deployment.yaml
```xml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice-one
  namespace: sample-ns
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
        image: jfrog.techworldwithmurali.in/tech/microservice-one:latest
```
##### service.yaml
```xml
apiVersion: v1
kind: Service
metadata:
  name: microservice-one
  namespace: sample-ns
spec:
  selector:
    app: microservice-one
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
    nodePort: 32000
  type: NodePort
```
### Step 4: Create a secret yaml file for Jfrog  credenatils using kubectl
```xml
kubectl create secret docker-registry jfrogcred \
--docker-server=https://jfrog.techworldwithmurali.in \
--docker-username=devops \
--docker-password=Techworld@2580 \
--namespace --dry-run=client -o yaml > secret.yaml
```
###### Output:
```xml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2pmcm9nLnRlY2h3b3JsZHdpdGhtdXJhbGkuaW4iOnsidXNlcm5hbWUiOiJkZXZvcHMiLCJwYXNzd29yZCI6IlRlY2h3b3JsZEAyNTgwIiwiYXV0aCI6IlpHVjJiM0J6T2xSbFkyaDNiM0pzWkVBeU5UZ3cifX19
kind: Secret
metadata:
  name: jfrogcred
  namespace: sample-ns
type: kubernetes.io/dockerconfigjson
```
```xml
imagePullSecrets:
- name: jfrogcred
```


### Step 9: Write the Jenkinsfile
  + ### Step 9.1: Clone the repository 
```xml
stage('Clone') {
            steps {
                git branch: 'deploy-to-eks-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
```

+ ### Step 9.5: Set Up AWS EKS Config
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
+ ### Step 9.5: Update Deployment File
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
+ ### Apply Kubernetes Manifests
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
### Step 10: Access java application through NodePort.
```xml
http://Node-IP:port/web-application
```
#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Pipeline job.
