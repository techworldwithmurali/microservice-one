+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins freestyle - Deploy to EKS fetching image from Jfrog repository.</br>

## Jenkins freestyle - Deploy to EKS fetching image from Jfrog repository.

### Prerequisites:
  + Jenkins is installed
  + Docker is installed
  + Jfrog is Installed
  + Github token generate
  + AWS EKS is created
  + kubectl is installed
  + AWS CLI is installed
  + Deployed the AWS ALB Ingress Controller"
  + Deployed ExternalDNS

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
### Step 4: Create the Build Jenkins job under microservice-one folder
```xml
Job Name: build 
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
### Step 8: Build and tag the Docker image
```xml
IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
docker build . --tag microservice-one:$IMAGE_TAG
docker tag microservice-one:$IMAGE_TAG jfrog.techworldwithmurali.in/tech/microservice-one:$IMAGE_TAG
```
### Step 9: Login to Jfrog in local
```xml
docker login -u devops -p Techworld@2580 jfrog.techworldwithmurali.in
```
### Step 10: tag and push to Jfrog artifactory
```xml
docker push jfrog.techworldwithmurali.in/tech/microservice-one:$IMAGE_TAG
```
### Step 11: Verify whether docker image is pushed or not in Jfrog Artifactory

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Jenkins Job 2: deploy-dev
### Step 1: Configure the AWS credentials or attach the IAM role to the Jenkins server.
### Step 2: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-jfrog-freestyle
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
    port: 80
    targetPort: 80
    nodePort: 32000
  type: NodePort

```
### Step 4: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
```
### Step 5: Apply the Kubernetes manifest files
```xml
cd kubernetes-yaml
kubectl apply -f .
```
### Step 6:Verify whether pods are running or not
```xml
kubectl get pods -n sample-ns
```
### Step 7: Create a secret file for Jfrog credenatils
```xml
kubectl create secret docker-registry jfrogcred \
--docker-server=https://jfrog.techworldwithmurali.in \
--docker-username=devops \
--docker-password=Techworld@2580 --dry-run=client -o yaml > secret.yaml
```
```xml
  imagePullSecrets:
  - name: jfrogcred
```
### Step 8: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one/
```
### Step 9: Deploy Ingress Resource for This Application
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

### Step 10: Check Whether Load Balancer, Rules, and DNS Records Are Created in Route 53

### Step 11: Access java application through DNS record Name.
```
https://myapp-dev.techworldwithmurali.in/microservice-one
```
#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Freestyle job.

