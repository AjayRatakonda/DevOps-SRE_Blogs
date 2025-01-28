# DevOps-SRE_Blogs
# **Day1:-**

**1)How to set up Kubernetes using K3s on an AWS Spot Instance and deploy an Nginx webpage in the Kubernetes cluster?**

Here’s a detailed, step-by-step guide on **how to set up Kubernetes using K3s on an AWS Ubuntu Spot Instance** and **deploy an Nginx webpage in the Kubernetes cluster**.

---

### **Steps**

#### **1. Launch an AWS Ubuntu Spot Instance**
  - **Log in to AWS Management Console.**

  - Navigate to the **EC2 Dashboard** → **Spot Requests**.
  
  - **Create a Spot Instance:**
   - AMI: **Ubuntu 22.04 or later**
   - Instance Type: **t2.medium** (or any preferred type)
   - Key Pair: Create new one or select an existing one.
   - Security Group: Allow the following:
     - SSH (port 22)
     - HTTP (port 80)
     - HTTPS (port 443)
     - Kubernetes NodePort range (30000–32767)

  - **Launch the Ubuntu instance** and connect via SSH:
  - By using below command we can connect to the instance.
   ```bash
   ssh -i <your-key.pem> ubuntu@<your-instance-public-ip>
   ```

---

#### **2. Install K3s on the Ubuntu Spot Instance**
1. Update the system Packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install Prerequisites `curl` and `wget` :
   ```bash
   sudo apt install curl wget -y
   ```

3. Install K3s (lightweight Kubernetes):
   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```
   

4. Verify K3s installation:
   ```bash
   kubectl version --short
   kubectl get nodes
   ```

   You should see the node in a `Ready` state.

---

#### **3. Deploy Nginx in Kubernetes**
1. Create a Deployment for Nginx:
   Save the YAML as `nginx-deployment.yaml`:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:latest
           ports:
           - containerPort: 80
   ```

   By using below command we can create the Deployment:
   ```bash
   kubectl apply -f nginx-deployment.yaml
   ```

2. Expose Nginx using a NodePort Service:
   Save the YAML as `nginx-service.yaml`:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-service
   spec:
     type: NodePort
     selector:
       app: nginx
     ports:
     - port: 80
       targetPort: 80
       nodePort: 30007
   ```

   By using below command we can create the Service:
   ```bash
   kubectl apply -f nginx-service.yaml
   ```

---

#### **4. Verify Nginx Deployment**
1. Check the Deployment and Pods and Service:
   ```bash
   kubectl get deployments
   kubectl get pods
   kubectl get svc
   ```

2. Now we can access Nginx in browser:

   Open the URL `http://<public-ip>:30007` in a web browser. You should see the default Nginx welcome page.

---

#### **5. Clean Up Resources**
To delete the Deployment and Service:
```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

### **Conclusion**
Successfully set up Kubernetes using K3s on an AWS Ubuntu Spot Instance and deployed an Nginx webpage in the Kubernetes cluster.
