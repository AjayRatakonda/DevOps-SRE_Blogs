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


# **Day2:-**

# 1. Introduction to Kubernetes

**1)What is kubernetes?**

  Kubernetes is an open-source container orchestration platform used for automating deployment,    scaling, and management of containerized applications.

**2)Why kubernetes?**

  Kubernetes is needed because it helps manage containerized applications easily. Without Kubernetes, handling multiple containers manually is complex and time-consuming.
  
  Simple Example:-
Imagine we are using an online shopping website running in containers. If traffic increases Kubernetes automatically adds more containers to handle the load. When traffic decreases, it removes extra containers to save resources.
That’s why Kubernetes is powerful and widely used.

**3)Explain kubernetes Architecture?**

  ### **Kubernetes Architecture**  

Kubernetes follows a **master-worker architecture** where the **Master Node** manages the cluster, and **Worker Nodes** run the applications.  

---

### **Main Components of Kubernetes Architecture**  

### **i) Master Node/Control Plane**  
The **Master Node/Control Plane** controls and manages the cluster. 

It has 4 main components:  

- **API Server** → It's a central component of k8s, which act as a bridge between the user and the cluster. All components of cluster will communicate with the API Server to perform their actions.
- **etcd (Cluster Database)** → etcd Stores all Kubernetes data (like cluster state & configurations).    
- **Scheduler** → Scheduler is component of kubernetes, which is responsible for assigning the pods to worker nodes and to maintain the desired state of the cluster.  
- **Controller Manager** →  Controller Manager constantly monitors the cluster and makes sure the desired state of the cluster is maintained. (e.g., restarts failed pods).

---

### **ii) Worker Nodes (Where Apps Run)**  
Worker Nodes **run the actual applications** inside containers. Each Worker Node has:  

- **Kubelet** → Communicates with the master and ensures containers are running properly.  
- **Kube Proxy** → It assigns dynamic IP to pods. It runs on each node to make sure that each pod will get unique IP.  
- **Container Runtime (Docker, containerd, CRI-O, etc.)** → Container Runtime responsible for running containers within pods.  

--- 

### **Pod, Node, and Cluster Concepts**
- **Pod**: The smallest unit in Kubernetes. A pod can contain one or more containers that share resources like storage and network.
- **Node**: A physical or virtual machine in the Kubernetes cluster that runs the pods.
- **Cluster**: A set of nodes (master and worker) that run your applications and manage them using Kubernetes.

---

## **Kubernetes Objects**

Kubernetes uses several **objects** to manage applications and resources in the cluster. Here are some of the key objects:

- **Pod**: The smallest unit in Kubernetes. A **pod** can have one or more containers that run your application and share resources like storage and networking.

- **ReplicaSet**: A ReplicaSet is a kubernetes resource that helps maintain a desired number of identical pods. It ensures that the specified number of replica pods are always running and it automatically creates or deletes pods to match the specified number.

- **Deployment**: A Deployment in Kubernetes is used to manage and update applications running in Pods. It ensures that the correct number of replicas (copies) of your application are running and can update them without downtime.

- **Service**: Exposes your application to the outside world or allows communication between pods inside the cluster. It creates a stable IP address for the pods.

- **Ingress**: Manages **external access** to your applications, typically over HTTP/HTTPS. It helps route traffic to different services based on URL paths or domain names.

- **ConfigMap**: Stores **non-sensitive configuration data** like environment variables, configuration files, or command-line arguments that can be used by pods.

- **Secret**: Stores **sensitive information** like passwords, API keys, and tokens securely. It ensures that this data is encrypted and not exposed in plain text.

- **Namespace**: A way to **group resources logically** within a cluster. It helps organize and isolate resources for different projects or teams.

---

### **Services(Expose Apps to the Outside World)**  
- **ClusterIP** 🏠 → Default, allows communication inside the cluster.  
- **NodePort** 🚪 → Exposes the app on a specific port of the node.  
- **LoadBalancer** ⚖️ → Distributes traffic across multiple nodes.  

---

### **Simple Diagram of Kubernetes Architecture**  

```
+-------------------+       +---------------------------+
|   Master Node    |       |      Worker Nodes        |
|------------------|       |-------------------------|
| API Server      |       | Kubelet | Pod | Pod     |
| Scheduler       |       | Kube Proxy | Pod | Pod  |
| Controller Mgr  |       | CRI (Docker)            |
| etcd (Database) |       |-------------------------|
+------------------+       +---------------------------+
        ⬇                          ⬇
  Controls & Manages       Runs applications in Pods
```

---

### **🌟 Summary**
✅ **Master Node** → Controls everything  
✅ **Worker Nodes** → Run applications  
✅ **Pods** → The smallest unit where apps run  
✅ **Services** → Allow access to applications  

With this simple architecture, **Kubernetes makes sure your applications run smoothly, scale automatically, and recover from failures!** 🚀🔥

  
