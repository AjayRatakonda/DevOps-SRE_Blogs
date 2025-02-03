Here’s a **detailed step-by-step guide** to setting up a **multi-node Kubernetes cluster using K3s**  

---

## **🛠 Step-by-Step: Install a Multi-Node Kubernetes Cluster using K3s**  

### **📌 Prerequisites**
- 1 **Master Node** (Control Plane)  
- 1 or more **Worker Nodes**  
- OS: **Ubuntu 22.04+** (or similar)  
- Each node must have **at least 2GB RAM and 2 vCPUs**  
- User should have **sudo** privileges  
- **Firewalls & Security Groups** should allow:
  - **Port 6443** (for API communication)
  - **Port 8472** (Flannel VXLAN traffic)
  - **Port 10250** (Kubelet metrics)
  - **Port 2379-2380** (etcd communication)

---

## **🚀 Step 1: Update All Nodes**
Run this on **all nodes** (Master & Workers):
```bash
sudo apt update && sudo apt upgrade -y
```
---

## **🌟 Step 2: Install K3s on the Master Node**
1️⃣ **Run the K3s Installer on Master**  
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
2️⃣ **Check if the cluster is running**  
```bash
sudo kubectl get nodes
```
3️⃣ **Retrieve the K3s Token** (Save this for Worker Nodes)  
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```
---

## **🔗 Step 3: Install K3s on Worker Nodes**
Run the following on **each worker node**, replacing `<MASTER_IP>` and `<TOKEN>`:
```bash
curl -sfL https://get.k3s.io | K3S_URL="https://<MASTER_IP>:6443" K3S_TOKEN="<TOKEN>" sh -
```
Verify the worker has joined:
```bash
sudo kubectl get nodes
```
You should see both **master and worker nodes** listed.

---

## **🔍 Step 4: Verify the Multi-Node Cluster**
Check cluster status:
```bash
sudo kubectl get nodes -o wide
```
Check pods running:
```bash
sudo kubectl get pods -A
```
---

## **🛠 Step 5: Deploy a Test Application**
Create a **Deployment & Service**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
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
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```
Apply it:
```bash
kubectl apply -f nginx.yaml
kubectl get svc
```
---

## **🧹 Step 6: Uninstall K3s (If Needed)**
On the **Master Node**:
```bash
/usr/local/bin/k3s-uninstall.sh
```
On **Worker Nodes**:
```bash
/usr/local/bin/k3s-agent-uninstall.sh
```
---

# **📖 README.md Format**
```md
# 🛠 Kubernetes Multi-Node Cluster Setup with K3s

## 📌 Prerequisites
- 1 Master Node, 1+ Worker Nodes
- Ubuntu 22.04+ (or similar)
- Open necessary ports (6443, 8472, 10250, 2379-2380)
- User should have sudo privileges

## 🚀 Step 1: Install K3s on Master
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
Check nodes:
```bash
kubectl get nodes
```
Get K3s Token:
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```

## 🔗 Step 2: Install K3s on Workers
```bash
curl -sfL https://get.k3s.io | K3S_URL="https://<MASTER_IP>:6443" K3S_TOKEN="<TOKEN>" sh -
```
Verify:
```bash
kubectl get nodes
```

## 🎯 Step 3: Deploy a Test Application
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
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
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```
Apply:
```bash
kubectl apply -f nginx.yaml
kubectl get svc
```

## 🧹 Step 4: Uninstall K3s (If Needed)
On Master:
```bash
/usr/local/bin/k3s-uninstall.sh
```
On Workers:
```bash
/usr/local/bin/k3s-agent-uninstall.sh
```

✅ **Now your multi-node Kubernetes cluster using K3s is ready!** 🚀
```

---

This setup will help you **practice K3s multi-node clusters with YAML automation**. Let me know if you need **any modifications or additional details**! 🚀🔥
