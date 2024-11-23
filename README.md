+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Freestyle Job - Deploy to EKS fetching image from AWS ECR.</br>

## Jenkins Freestyle Job - Deploy to EKS fetching image from AWS ECR.

### Prerequisites:
+ Jenkins is installed
+ Docker is installed
+ AWS cli is installed
+ AWS EKS is created
+ IAM user is created.  User name: dev
+ Github token generate

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration

### Step 2: Create the AWS ECR  repository
```xml
Name: microservice-one
```

### Step 3: Create the Jenkins Free style job
```xml
Job Name: deploy-to-eks-ecr-freestyle
```
### Step 4: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-ecr-freestyle
```
### Step 5: Invoke the top level maven targets
```xml
clean package
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
### Step 7: Build and tag the Docker image
```xml
docker build . --tag microservice-one:$BUILD_NUMBER
docker tag microservice-one:$BUILD_NUMBER 266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
```
### Step 11: Configure the AWS credentials or attach the IAM role to the Jenkins server.
```xml
aws configure
```
### Step 8: login to AWS ECR
```xml
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 266735810449.dkr.ecr.us-east-1.amazonaws.com
```
### Step 9: Push to AWS ECR
```xml
docker push 266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
```
### Step 10: Verify whether docker image is pushed or not in AWS ECR
### Step 11: Write the Kubernetes Deployment and Service manifest files.
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
### Step 12: Configure the AWS credenatils or Add the IAM role in master or slave node

### Step 13: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
```
### Step 14: Apply the Kubernetes manifest files
```xml
cd kubernetes
kubectl apply -f .

kubectl set image deployment/microservice-one microservice-one=266735810449.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$BUILD_NUMBER
```
### Step 15:Verify whether pods are running or not
```xml
kubectl get pods -A
```
### Step 16: Access java application through NodePort.
```xml
http://Node-IP:port/microservice-one/
```

### Step 17: Deploy Ingress Resource for This Application
```xml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: sample-ingress-dev
  namespace: sample-ns
  annotations:
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/tags: app=techworldwithmurali,Team=DevOps
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:533267221649:certificate/00cbdeae-a854-412c-87dd-a79eae85a402
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    alb.ingress.kubernetes.io/security-groups: sg-05a2c24577d05d379

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
                  number: 80

```

### Step 18: Check Whether Load Balancer, Rules, and DNS Records Are Created in Route 53

### Step 19: Access java application through DNS record Name.
```
https://myapp-dev.techworldwithmurali.in/microservice-one
```

#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Freestyle job.

