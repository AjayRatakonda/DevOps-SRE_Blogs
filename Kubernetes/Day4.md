Here’s a **detailed step-by-step guide** to setting up a **multi-node Kubernetes cluster using K3s**  

---

## **Step-by-Step: Install a Multi-Node Kubernetes Cluster using K3s**  

### **Prerequisites**
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

## **Step 1: Update All Nodes**
Run this on **all nodes** (Master & Workers):
```bash
sudo apt update && sudo apt upgrade -y
```
---

## **Step 2: Install K3s on the Master Node**
 **Run the K3s Installer on Master**  
```bash
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644
```
 **Check if the cluster is running**  
```bash
sudo kubectl get nodes
```
 **Retrieve the K3s Token** (Save this for Worker Nodes)  
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```
---

## **🔗 Step 3: Install K3s on Worker Nodes**
Run the following on **each worker node**, replacing `<MASTER_IP>` and `<TOKEN>`:
```bash
curl -sfL https://get.k3s.io | K3S_URL="https://172.31.13.49:6443" K3S_TOKEN="K10b944b48298edf1ff07cbd53b58ce15867cfe9d5f49ecb85d26862367d10c1571::server:e6ffa4fd23df815de1d991e7e03efe0b" sh -
```
Verify the worker has joined:
```bash
sudo kubectl get nodes
```
You should see both **master and worker nodes** listed.

---

## **Step 4: Verify the Multi-Node Cluster**
Check cluster status:
```bash
sudo kubectl get nodes -o wide
```
Check pods running:
```bash
sudo kubectl get pods -A
```
---

## **Step 6: Uninstall K3s (If Needed)**
On the **Master Node**:
```bash
/usr/local/bin/k3s-uninstall.sh
```
On **Worker Nodes**:
```bash
/usr/local/bin/k3s-agent-uninstall.sh
```
---
