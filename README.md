+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins freestyle - Deploy to EKS fetching image from Jfrog repository.</br>

## Jenkins freestyle - Deploy to EKS fetching image from Jfrog repository.

### Prerequisites:
  + Jenkins is installed
  + Docker is installed
  + Github token generate
  + AWs EKS is created
  + kubectl is installed
  + ZAWS CLI is installed

### Step 1: Install and configure the jenkins plugins
  + git
  + maven integration
  
### Step 2: Create the user in Jfrog
```xml
UserName: moole
Password: Techworld@2580
```
### Step 3: Create the docker repository in Jfrog
```xml
Repository Name: microservices
```
### Step 4: Create the Jenkins job
```xml
Job Name: deploy-to-eks-jfrog-freestyle
```

### Step 5: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/java-application.git
Branch : deploy-to-eks-jfrog-freestyle
```

### Step 6: Invoke the top level maven targets
```xml
clean package
```
### Step 7: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 8: Build the Docker image
```xml
docker build . --tag microservice-one:$BUILD_NUMBER
```
### Step 9: Login to Jfrog in local
```xml
docker login -u moole -p Techworld@2580 a0twcdxxwofaz.jfrog.io
```
### Step 10: tag and push to Jfrog artifactory
```xml
docker tag microservice-one:$BUILD_NUMBER a0twcdxxwofaz.jfrog.io/microservices/microservice-one:$BUILD_NUMBER
docker push a0twcdxxwofaz.jfrog.io/microservices/microservice-one:$BUILD_NUMBER
```
### Step 11: Verify whether docker image is pushed or not in Jfrog Artifactory
### Step 12: Configure the AWS credenatils in Jenkins Server
```xml
export AWS_ACCESS_KEY_ID="AKIARSNVB6PUUCT7RT74"
export AWS_SECRET_ACCESS_KEY="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"
export AWS_DEFAULT_REGION="us-east-1"
```
### Step 13: Write the Kubernetes Deployment and Service manifest files.
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
### Step 14: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
```
### Step 15: Apply the Kubernetes manifest files
```xml
cd kubernetes
kubectl apply -f .

kubectl set image deployment/microservice-one microservice-one=mmreddy424/microservice-one:latest
```
### Step 16:Verify whether pods are running or not
```xml
kubectl get pods -A
```
### Step 17: Create a secret file for Jfrog credenatils
```xml
kubectl create secret docker-registry jfrogcred \
--docker-server=https://a0twcdxxwofaz.jfrog.io \
--docker-username=moole \
--docker-password=Techworld@2580 --dry-run=client -o yaml > secret.yaml
```
```xml
  imagePullSecrets:
  - name: jfrogcred
```
### Step 18: Access java application through NodePort.
```xml
http://Node-IP:port/web-application
```
#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Freestyle job.

