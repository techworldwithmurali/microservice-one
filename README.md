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
+ Deployed the AWS ALB Ingress Controller"
+ Deployed ExternalDNS

### Step 1: Install and configure the jenkins plugins
 + git
 + maven integration

### Step 2: Create the Docker repository
```xml
Name: microservice-one
```

### Step 3: Create the Build Jenkins job under microservice-one folder
```xml
Job Name: build
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
FROM tomcat:9.0.96-jdk17
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
docker login -u mmreddy424 -p Docker@2580
```
### Step 9: Push to DockerHub
```xml
docker push mmreddy424/microservice-one:latest
```
### Step 10: Verify whether docker image is pushed or not in DockerHub
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Jenkins Job 2: deploy-dev
### Step 1: Attach the IAM role to the Jenkins server
### Step 2: Configure the git repository
```xml
GitHub Url: https://github.com/techworldwithmurali/microservice-one.git
Branch : deploy-to-eks-dockerhub-freestyle
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
    nodePort: 30160
  type: NodePort

```
### Step 4: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
kubectl get nodes
```
### Step 5: Apply the Kubernetes manifest files
```xml
cd kubernetes
kubectl apply -f .

```
### Step 6:Verify whether pods are running or not
```xml
kubectl get pods -n sample-ns
```
### Step 7: Create a secret file for Dockerhub credenatils
```xml
kubectl create secret docker-registry dockerhubcred \
--docker-server=https://index.docker.io/v1/ \
--docker-username=mmreddy424 \
--docker-password=Docker@2580 \
--docker-email=techworldwithmurali@gmail.com
--namespace sample-ns
```
```xml
imagePullSecrets:
- name: dockerhubcred
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
https://myapp-dev.techworldwithmurali.in/microservice-one/
```

### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS) through Jenkins Freestyle job.

