+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>
+ <b>Description:</b> Below are the steps outlined for Manual Process - Deploy an Application on Kubernetes Using Helm Chart.</br>

### Steps to Deploy an Application on Kubernetes Using Helm Chart

**Step 1**: Clone the Helm chart repository.  
```bash
git clone https://github.com/techworldwithmurali/helmchart.git
cd <repository-directory>
```
---

**Step 2**: Write the Helm chart files.  
- Create and configure the required files:  
  - `templates/Deployment.yaml`
  - `templates/Service.yaml`
  - `values.yaml`  
- Customize the `values.yaml` file with your application's image, replicas, and other configurations.  

Example `values.yaml`:  
```yaml
image:
  repository: <dockerhub-image-repo>
  tag: <image-tag>
service:
  type: NodePort
  port: 80
```

---

**Step 3**: Install the Helm chart.  
```bash
helm install microservice-one . --namespace user-management --create-namespace
```

---

**Step 4**: Verify the deployment.  
Check the list of Helm releases:  
```bash
helm list -n user-management
```

---

**Step 5**: Creat ethe secret and add in values.yaml
1. create teh secret using below comamnd

```yaml
kubectl create secret docker-registry jfrogcred \
--docker-server=https://jfrog.techworldwithmurali.in \
--docker-username=devops \
--docker-password=Techworld@2580

```
2. Update `values.yaml` to use the secret.
```yaml
imagePullSecrets:
- name: dockerhubcred
```
---

**Step 6**: Upgrade the Helm chart.  
```bash
helm upgrade microservice-one . -n user-management
```

---

**Step 7**: Access the application via NodePort.  
1. Check the NodePort service:  
   ```bash
   kubectl get svc -n user-management
   ```
2. Use the `NodePort` and your Kubernetes node's IP to access the application:  
   ```
   http://<node-ip>:<node-port>
   ```

---

**Step 8:** Create the Ingress Helm Chart  

1. **Clone the Ingress Helm Chart**:  
   Clone an existing Ingress Helm chart template or initialize a new one:  
   ```bash
   git clone https://github.com/techworldwithmurali/ingress.git 
   cd ingress
   ```

2. **Define the Ingress Resource**:  
   Create or edit the `templates/ingress-internal.yaml` file. Add the desired ingress configuration:  
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
    {{- range .Values.ingress.hosts }}
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

3. **Update `values.yaml`**:  
   Add the ingress-related configurations to `values.yaml`:  
```yaml
internal:
  enabled: true
  className: "alb"  # Use your ingress class name (e.g., "alb")
  annotations:
    kubernetes.io/ingress.class: alb
  hosts:
    - hostname: user-managment-dev.techworldwithmurali.in
      service: user-managment
      port: 80
   ```

4. **Install the Ingress Helm Chart**:  
   Deploy the Helm chart with the specified release name and namespace:  
   ```bash
   helm install dev-user-management . --namespace user-management
   ```

5. **Verify the Ingress Resource**:  
   Check if the ingress resource was created successfully:  
   ```bash
   kubectl get ingress -n user-management
   ```
---   

**Step 9**: Access the application via DNS.  
Ensure the DNS record is pointing to your ingress controller's external IP. Then access your application at:  
```
https://user-managment-dev.techworldwithmurali.in
```
---
**Step 10**: Uninstall the Helm chart.  
```bash
helm uninstall microservice-one -n user-management
helm uninstall dev-user-management  -n user-management
```
---
**Congratulations!**  
You have successfully deployed the application on Kubernetes using a Helm chart, with the Docker image fetched from Docker Hub.
