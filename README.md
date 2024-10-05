+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle - Deploy to EKS fetching image from DockerHub.</br>

## Jenkins Freestyle - Deploy to EKS fetching image from DockerHub.

### Prerequisites:
+  Jenkins is installed
+  Docker is installed
+  AWS EKS is created
+  kubectl is installed
+  AWS CLI installed
+  Github token generate

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration

### Step 2: Create the Docker repository
```xml
Name: microservice-one
```

### Step 3: Create the Jenkins job
```xml
Job Name: deploy-to-eks-dockerhub
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-dockerhub-freestyle
```
### Step 5: Invoke the top level maven targets
```xml
clean package
```
### Step 6: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 7: Build and tag the Docker image
```xml
docker build . --tag microservice-one:latest
docker tag microservice-one:latest mmreddy424/microservice-one:latest
```
### Step 8: login to DockerHub
```xml
docker login -u mmreddy424 -p Docker@123
```
### Step 9: Push to DockerHub
```xml
docker push mmreddy424/microservice-one:latest
```
### Step 10: Verify whether docker image is pushed or not in DockerHub
### Step 11: Configure the AWS credenatils in Jenkins Server
```xml
aws configure
```
### Step 12: Write the Kubernetes Deployment and Service manifest files.
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
        image: mmreddy424/microservice-one:latest
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
### Step 12: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
```
### Step 13: Apply the Kubernetes manifest files
```xml
cd kubernetes
kubectl apply -f .

kubectl set image deployment/microservice-one microservice-one=mmreddy424/microservice-one:latest
```
### Step 14:Verify whether pods are running or not
```xml
kubectl get pods -A
```
### Step 15: Create a secret file for Dockerhub credenatils
```xml
kubectl create secret docker-registry dockerhubcred \
--docker-server=https://index.docker.io/v1/ \
--docker-username=mmreddy424 \
--docker-password=Docker@123 \
--docker-email=techworldwithmurali@gmail.com
```
```xml
imagePullSecrets:
- name: dockerhubcred
```
### Step 16: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one
```
### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Freestyle job.

