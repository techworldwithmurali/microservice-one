+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for manually Deploy to EKS fetching image from AWS ECR.</br>

## Manually - Deploy to EKS fetching image from AWS ECR.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Docker is installed
+ AWS ECR repository is created
+ AWS EKS is created
+ IAM User is created
+ kubectl is installed
+ aws cli is installed
+ Deployed the AWS ALB Ingress Controller
+ Deployed ExternalDNS

### Step 1: Clone the repository
  
```xml
  github url: https://github.com/techworldwithmurali/microservice-one.git
  Branch Name: deploy-to-eks-ecr
```
### Step 2: build the code
```xml
mvn package
```
### Step 3: Create the repository in AWS ECR
```xml
Repository Name: microservice-one
```
### Step 4: Write the Dockerfile
```xml
FROM tomcat:9.0.96-jdk17
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 5: Build and tag the Docker image.
```xml
docker build . --tag microservice-one:latest

docker tag microservice-one:latest 266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
### Step 6: Login to  AWS ECR in local
```xml
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 266735810449.dkr.ecr.us-east-1.amazonaws.com
```
### Step 7: Push the docker image to AWS ECR

### Step 8: Verify whether docker image is pushed or not in AWS ECR
```xml
docker push 266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
```
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
        image: 266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:latest
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
    port: 8080
    targetPort: 8080
    nodePort: 32000
  type: NodePort

```
### Step 10: Update the AWS ECR image in deployment.yaml
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
### Step 15: Deploy Ingress Resource for This Application
```xml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dev-ingress
  namespace: dev
  annotations:
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/tags: app=techworldwithmurali,Team=DevOps
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:266735810449:certificate/8a7cbcb1-774c-463f-ab3e-476437028686
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    alb.ingress.kubernetes.io/security-groups: sg-05a2c24577d05d379

spec:
  ingressClassName: alb
  rules:
    - host: microservice-one-dev.techworldwithmurali.in
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

### Step 16: Check Whether Load Balancer, Rules, and DNS Records Are Created in Route 53

### Step 17: Access java application through DNS record Name.
```
https://microservice-one-dev.techworldwithmurali.in/microservice-one/
```


#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS).
