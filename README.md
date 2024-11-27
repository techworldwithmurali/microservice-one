+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Jenkins Pipeline Job - Deploy an Application on Kubernetes Using Helm Chart.</br>

### Steps to Deploy an Application on Kubernetes Using Helm Chart

---

### **Prerequisites**
1. **Jenkins**: Installed and accessible.
2. **Helm**: Installed and configured.
3. **AWS EKS**: A running cluster.
4. **kubectl**: Installed and configured to interact with the EKS cluster.
5. **AWS CLI**: Installed and authenticated.
6. **GitHub Token**: Generated for repository access.
7. **AWS ALB Ingress Controller**: Deployed to the cluster.
8. **ExternalDNS**: Configured for managing DNS entries.

---

### **Steps**

#### 1. **Create Jenkins Pipeline Job**
- **Job Name:** `dev-deploy`
- **Folder Name:** `microservice-one`

#### 2. **Git Configuration**
- **GitHub URL:** `https://github.com/techworldwithmurali/helmchart.git`
- **Branch:** `dev`

#### 3. **Prepare Helm Charts**
- Create and configure:
  - `templates/Deployment.yaml`
  - `templates/Service.yaml`
  - `values.yaml`

Example `values.yaml`:
```yaml
image:
  repository: <dockerhub-image-repo>
  tag: <image-tag>
service:
  type: NodePort
  port: 80
```

#### 4. **Clone the repository**
```bash
stage('Clone') {
            steps {
                git branch: 'dev', credentialsId: 'github-cred', url: 'https://github.com/techworldwithmurali/helmchart.git'
            }
        }
```
#### 5. **Connect to the EKS Cluster**
Use the AWS CLI to update the kubeconfig for your EKS cluster:
```bash
 stage('Connect to the EKS Cluster') {
            steps {
                script {
                    // Ensure AWS CLI is configured with the right credentials before this step
                    sh '''
                    aws eks update-kubeconfig --name dev-cluster --region us-east-1
                    '''
                }
            }
        }
```

#### 6. **Deploy Helm Chart**
Install the application using Helm:
```bash
stage('Deploy the Application') {
            steps {
                script {
                    // Deploy the application with the provided parameters
                    sh '''
                    helm upgrade --install $RELEASE_NAME . --namespace $namespace --create-namespace --set image.tag=$ImageTag --force --wait --timeout 600s
                    '''
                }
            }
        }
```

#### 7. **Create DockerHub Secret**
Create a Kubernetes secret for DockerHub credentials:
```bash
kubectl create secret docker-registry dockerhubcred \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<your-username> \
  --docker-password=<your-password> \
  --namespace user-management
```
Update `values.yaml` to reference the secret:
```yaml
imagePullSecrets:
  - name: dockerhubcred
```

#### 8. **Upgrade Helm Chart**
Run the Jenkins job to upgrade the Helm chart.

#### 9. **Access Application via NodePort**
Find the NodePort service and access the application:
```bash
kubectl get svc -n user-management
```
Access at:
```
http://<node-ip>:<node-port>
```

---

### **Ingress Setup**

#### 1. **Create Jenkins Pipeline Job**
- **Job Name:** `dev-ingress`
- **Folder Name:** `ingress`

#### 2. **Git Configuration**
- **GitHub URL:** `https://github.com/techworldwithmurali/ingress.git`
- **Branch:** `dev`

#### 3. **Define Ingress Resource**
Edit `templates/ingress-internal.yaml` for internal ingress configurations:
```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ .Release.Name }}-internal
  {{- if .Values.namespace }}
  namespace: {{ .Values.namespace }}
  {{- end }}
  labels:
    app.kubernetes.io/name: {{ .Release.Name }}
    helm.sh/chart: {{ .Chart.Name }}
    {{- if .Chart.AppVersion }}
    app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
    {{- end }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
  {{- with .Values.ingress.annotations }}
  annotations:
    {{- toYaml . | nindent 4 }}
  {{- end }}
spec:
  {{- if .Values.ingress.className }}
  ingressClassName: {{ .Values.ingress.className }}
  {{- end }}
  rules:
    {{- range .Values.internal.hosts }}
    - host: {{ .hostname }}
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{ .service }}
                port:
                  number: {{ .port }}
    {{- end }}
{{- end }}

```

#### 4. **Update `values.yaml`**
Add ingress configurations:
```yaml
internal:
  enabled: true
  className: "alb"
  hosts:
    - hostname: user-management-dev.techworldwithmurali.in
      service: user-management
      port: 80
```
#### 5. **Clone the repository**
```bash
stage('Clone') {
            steps {
                git branch: 'dev', credentialsId: 'github-cred', url: 'https://github.com/techworldwithmurali/ingress.git'
            }
        }
```
#### 6. **Connect to the EKS Cluster**
Use the AWS CLI to update the kubeconfig for your EKS cluster:
```bash
 stage('Connect to the EKS Cluster') {
            steps {
                script {
                    // Ensure AWS CLI is configured with the right credentials before this step
                    sh '''
                    aws eks update-kubeconfig --name dev-cluster --region us-east-1
                    '''
                }
            }
        }
```

#### 7. **Install Ingress Helm Chart**
Install the application using Helm:
```bash
stage('Deploy the Application') {
            steps {
                script {
                    // Deploy the application with the provided parameters
                    sh '''
                    helm upgrade --install $RELEASE_NAME . --namespace $namespace --create-namespace --force --wait --timeout 600s
                    '''
                }
            }
        }
```

#### 8. **Verify Ingress Resource**
Check ingress resource status:
```bash
kubectl get ingress -n user-management
```

#### 9. **Access Application via DNS**
Ensure DNS points to the ingress controller. Access the application at:
```
https://user-management-dev.techworldwithmurali.in
```

---

### **Uninstallation**
To clean up the deployment:
```bash
helm uninstall microservice-one -n user-management
helm uninstall dev-user-management -n user-management
```

---

### **Congratulations!**
You have successfully deployed the application on Kubernetes using a Helm chart, with the Docker image fetched from Docker Hub using Jenkins Pipeline job..
