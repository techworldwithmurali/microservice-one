+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Deploy to EKS fetching image from DockerHub.</br>

## Jenkins Pipeline - Deploy to EKS fetching image from DockerHub.

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
 + Pipeline: AWS Steps

### Step 2: Create the Docker repository
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
        image: mmreddy424/web-application:latest
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
### Step 5: Create a secret yaml file for Dockerhub credenatils using kubectl
```xml
 kubectl create secret docker-registry dockerhubcred \
--docker-server=https://index.docker.io/v1/ \
--docker-username=mmreddy424 \
--docker-password=Docker@123 \
--docker-email=techworldwithmurali@gmail.com --dry-run=client -o yaml > secret.yaml
```
###### Output:
```xml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOnsidXNlcm5hbWUiOiJtbXJlZGR5NDI0IiwicGFzc3dvcmQiOiJEb2NrZXJAMTIzIiwiZW1haWwiOiJ0ZWNod29ybGR3aXRobXVyYWxpQGdtYWlsLmNvbSIsImF1dGgiOiJiVzF5WldSa2VUUXlORHBFYjJOclpYSkFNVEl6In19fQ==
kind: Secret
metadata:
  name: dockerhubcred
type: kubernetes.io/dockerconfigjson

```
```xml
imagePullSecrets:
- name: dockerhubcred
```
### Step 6: Create the Jenkins Pipeline job
```xml
Job Name: deploy-to-eks-dockerhub-jenkins-pipeline
```
### Step 7: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-dockerhub-jenkinsfile
```


### Step 8: Write the Jenkinsfile
  + ### Step 8.1: Clone the repository 
```xml
stage('Clone the repository'){
        steps{
          git branch: 'deploy-to-eks-dockerhub-jenkinsfile', credentialsId: 'Github_credentails', url: 'https://github.com/techworldwithmurali/microservice-one.git'
          
        } 
      }
```
  + ### Step 8.2: Build the code
```xml
stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
```
  + ### 8.3: Build Docker Image
```xml
stage('Build Docker Image') {
            steps {
                sh '''
               docker build . --tag microservice-one:$BUILD_NUMBER
               docker tag microservice-one:$BUILD_NUMBER mmreddy424/microservice-one:$BUILD_NUMBER
                
                '''
                
            }
        }
   
```
+ ### 8.4 Push Docker Image
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'dockerhub_crdenatils', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
       
                    sh '''
                    docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD
                        docker push mmreddy424/microservice-one:$BUILD_NUMBER
                    '''
                }
            } 
            
        }
```
+ ### 8.5 Deploy to AWS EKS
```xml
stage('Deployto AWS EKS') {
            steps {
                // configure AWS credentials
               withAWS(credentials: 'AWS', region: 'us-east-1') { 
                    sh '''
                     // configure kubectl to access EKS cluster
                     aws eks update-kubeconfig --name dev-cluster --region us-east-1

                    // Apply the kubernetes YAML files to EKS cluster
                     cd kubernetes-yaml
                    kubectl apply -f . 
                    kubectl set image deployment/microservice-one microservice-one=mmreddy424/microservice-one:$BUILD_NUMBER
                }
           
        }
            
        }
```
### Step 9: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one
```
### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Pipeline job.

