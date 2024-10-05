+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually Deploy to EKS fetching image from Jfrog repository.</br>

## Manually - Deploy to EKS fetching image from Jfrog repository.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Docker is installed
+ Jfrog docker repository is created
+ AWS EKS is created
+ IAM User is created
+ kubectl is installed
+ AWS cli is installed

### Step 1: Clone the repository
  
```xml
  github url: https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: deploy-to-eks-jfrog
```
### Step 2: build the code
```xml
mvn package
```
### Step 3.1: Create the user in Jfrog
```xml
UserName: moole
Password: Techworld@2580
```
### Step 3.2: Create the docker repository in Jfrog
```xml
Repository Name: microservices
```
### Step 4: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 5: Build and tag the Docker image
```xml
docker build . --tag microservice-one:latest
```
### Step 6: Login to Jfrog in local
```xml
docker login -umoole a0twcdxxwofaz.jfrog.io
```
### Step 7: Push the docker image to Jfrog Artifactory.
```xml
docker tag microservice-one:latest a0twcdxxwofaz.jfrog.io/microservices/microservice-one:latest

docker push a0twcdxxwofaz.jfrog.io/microservices/microservice-one:latest
```
### Step 8: Verify whether docker image is pushed or not in Jfrog Artifactory
### Step 9 : Write the Kubernetes Deployment and Service manifest files.
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
        image: a0twcdxxwofaz.jfrog.io/microservices/microservice-one:latest
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
### Step 10: Update the Jfrog artifactory docker image in deployment.yaml
### Step 11: Configure  to the AWS CLI using Access key ID & Secret access key
```xml
aws configure
```
### Step 12: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
````
### Step 13: Apply the Kubernetes manifest files
```
kubectl apply -f .
```
### Step 14: Verify wether pods are running or not
```
kubectl get pods -A
```
### Step 15: Create a secret file for Jfrog credenatils
```xml
kubectl create secret docker-registry jfrogcred \
--docker-server=https://a0twcdxxwofaz.jfrog.io \
--docker-username=moole \
--docker-password=Techworld@2580
```
```xml
  imagePullSecrets:
  - name: jfrogcred

```
### Step 16: Access Java application through NodePort.
```
http://Node-IP:port/microservice-one
```


#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS).

