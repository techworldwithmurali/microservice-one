# micro-service-one

+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> https://techworldwithmurali.com </br>
+ <b>Youtube Channel:</b> Tech World With Murali</br>

### Steps to Deploy an Application on Kubernetes Using Helm Chart

**Step 1**: Clone the Helm chart repository.  
```bash
git clone <repository-url> -b main
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

**Step 5**: Add secrets and update values.  
1. Write a `templates/Secret.yaml` file with required credentials or sensitive information.  
Example:  
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: user-management
type: Opaque
data:
  username: <base64-encoded-username>
  password: <base64-encoded-password>
```
2. Update `values.yaml` to use the secret.  
3. Upgrade the Helm chart to apply changes.

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

**Step 8**: Add an ingress resource.  
1. Write an `Ingress.yaml` file:  
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: app-ingress
     namespace: user-management
   spec:
     rules:
       - host: <your-dns-name>
         http:
           paths:
             - path: /
               pathType: Prefix
               backend:
                 service:
                   name: <service-name>
                   port:
                     number: 80
2. Apply the ingress resource:  
   ```bash
   kubectl apply -f templates/Ingress.yaml -n user-management
   ```

**Step 9**: Access the application via DNS.  
Ensure the DNS record is pointing to your ingress controller's external IP. Then access your application at:  
```
http://<your-dns-name>
```

**Step 10**: Uninstall the Helm chart.  
Remove the deployment when it’s no longer needed:  
```bash
helm uninstall microservice-one -n user-management
```

**Congratulations!**  
You have successfully deployed the application on Kubernetes using a Helm chart, with the Docker image fetched from Docker Hub.
