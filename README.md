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
   kubectl version
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
   **Example Output**

   ![image](https://github.com/user-attachments/assets/07320370-d3e6-49c3-a3a1-1f6fc55a1ea7)

   
3. Expose Nginx using a NodePort Service:
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
   **Example Output**

   ![Screenshot 2025-01-29 173135](https://github.com/user-attachments/assets/9756b846-eda1-476b-b309-d055acec8c08)

---

#### **4. Verify Nginx Deployment**
1. Check the Deployment and Pods and Service:
   ```bash
   kubectl get deployments
   kubectl get pods
   kubectl get svc
   ```
   **Example Output:-**
   
   ![Screenshot 2025-01-29 173430](https://github.com/user-attachments/assets/09c4e53c-8570-4e06-ab94-372d08a193a4)


3. Now we can access Nginx in browser:

   Open the URL `http://<public-ip>:30007` in a web browser. You should see the default Nginx welcome page.

**Example Output:-**

![Screenshot 2025-01-29 173608](https://github.com/user-attachments/assets/bc3ad3d6-8433-491b-8212-f73127c68f6a)

---

#### **5. To Delete Resources**
To delete the Deployment and Service:
```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```
**Example Output:-**

![Screenshot 2025-01-29 173936](https://github.com/user-attachments/assets/cccb7139-b37e-4db0-8610-012b6feab099)
![Screenshot 2025-01-29 173952](https://github.com/user-attachments/assets/336b6759-caf0-485b-8655-2a9c07b6220c)


### **Conclusion**
Successfully set up Kubernetes using K3s on an AWS Ubuntu Spot Instance and deployed an Nginx webpage in the Kubernetes cluster.

---





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

- **Namespace**: A **Namespace** in Kubernetes is like a **separate workspace** inside a cluster. It helps organize and manage different resources **without mixing them up**.  

For example:  
- `dev` namespace → for development  
- `test` namespace → for testing  
- `prod` namespace → for production  

This makes it easier to manage large applications and teams.

---

## **Working with Pods and Deployments**  

### **Creating a Pod using `kubectl run`**  
By using below command we can create pod:  

```sh
kubectl run tomcat --image=tomcat --port=8080
```

**Example Output:-**

![Screenshot 2025-01-29 162157](https://github.com/user-attachments/assets/7961aec2-99aa-4376-be9a-b87ebd407c48)


**Check the Pod status:** 

By using below command we can see the pods of default namespace.

```sh
kubectl get pods
```

**Example Output:-**

![Screenshot 2025-01-29 162224](https://github.com/user-attachments/assets/1928b209-f50a-4e02-957c-56c64677a3d0)


**Describe the Pod for more details:**  

By using below command we can see the all details of the pod.

```sh
kubectl describe pod tomcat
```
**Example Output:-**

 ![Screenshot 2025-01-29 170324](https://github.com/user-attachments/assets/d21bd35b-c679-4361-b009-c6308855d613)


**Delete the Pod:**

By using below command we can delete the pod.

```sh
kubectl delete pod my-nginx
```

**Example Output:-**

![image](https://github.com/user-attachments/assets/643acb41-c367-48a4-93a8-e1bb0d43d269)

---

  
