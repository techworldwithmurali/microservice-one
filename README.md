+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline - Deploy to EKS fetching image from DockerHub.</br>

## Jenkins Pipeline - Deploy to EKS fetching image from DockerHub.

### Prerequisites:
  + Jenkins is installed
  + Docker is installed
  + Github token generate
  + AWS EKS is created
  + kubectl is installed
  + AWS CLI is installed
  + Deployed the AWS ALB Ingress Controller"
  + Deployed ExternalDNS

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration

### Step 2: Create the Docker repository
```xml
Name: microservice-one
```
### Step 3:  Create the Build Jenkins job under microservice-one folder
```xml
Job Name: build
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-dockerhub-jenkinsfile
```
### Step 5: Write the Jenkinsfile
  + ### Step 5.1: Clone the repository 
```xml
stage('Clone the repository'){
        steps{
          git branch: 'deploy-to-eks-dockerhub-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
          
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
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
  + ### 6.1: Build Docker Image
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
+ ### 6.2 Push Docker Image
```xml
stage('Push Docker Image') {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
       
                    sh '''
                   IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                    docker push mmreddy424/microservice-one:$IMAGE_TAG
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
        image: mmreddy424/microservice-one:latest
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
### Step 4: Write the Jenkinsfile
  + ### Step 4.1: Clone the repository 
```xml
stage('Clone') {
            steps {
                git branch: 'deploy-to-eks-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
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
### Step 5: Create a secret yaml file for Dockerhub credenatils using kubectl
```xml
 kubectl create secret docker-registry dockerhubcred \
--docker-server=https://index.docker.io/v1/ \
--docker-username=mmreddy424 \
--docker-password=Docker@2580 \
--docker-email=techworldwithmurali@gmail.com \
--namespace sample-ns --dry-run=client -o yaml
```
###### Output:
```xml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOnsidXNlcm5hbWUiOiJtbXJlZGR5NDI0IiwicGFzc3dvcmQiOiJEb2NrZXJAMjU4MCIsImVtYWlsIjoidGVjaHdvcmxkd2l0aG11cmFsaUBnbWFpbC5jb20iLCJhdXRoIjoiYlcxeVpXUmtlVFF5TkRwRWIyTnJaWEpBTWpVNE1BPT0ifX19
kind: Secret
metadata:
  name: dockerhubcred
  namespace: sample-ns
type: kubernetes.io/dockerconfigjson

```
```xml
imagePullSecrets:
- name: dockerhubcred
```
### Step 6: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one/
```
### Step 7: Deploy Ingress Resource for This Application
```xml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress-dev
  namespace: sample-ns
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
    - host: myapp-dev.techworldwithmurali.in
      http:
        paths:
          - path: /microservice-one/
            pathType: Prefix
            backend:
              service:
                name: microservice-one
                port:
                  number: 8080

```

### Step 8: Check Whether Load Balancer, Rules, and DNS Records Are Created in Route 53

### Step 9: Access java application through DNS record Name.
```
https://myapp-dev.techworldwithmurali.in/microservice-one/
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

