## **How to install kubernetes using KIND On Ubuntu**

## **Install Kubernetes using KIND on Ubuntu 22.04**  

KIND (Kubernetes IN Docker) is a tool for running Kubernetes clusters using Docker containers instead of VMs. It's lightweight and great for testing and development.

---

## **Prerequisites**
Before installing KIND, ensure you have:
1. **Ubuntu 22.04 Server**
2. **Docker installed**
3. **kubectl installed**
4. **At least 2 vCPUs and 2GB RAM** (4GB recommended)
5. **User with sudo privileges**

---

## **Step 1: Update Your System**
Run the following command to update and upgrade your system:
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **Step 2: Install Docker**
KIND requires Docker to run Kubernetes clusters as containers.

 Install Docker:
```bash
sudo apt install -y docker.io
```

 Enable and start Docker:
```bash
sudo systemctl enable --now docker
```

 Verify Docker installation:
```bash
docker --version
```

 Add your user to the `docker` group (optional, to run Docker without sudo):
```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## **Step 3: Install kubectl (Kubernetes CLI)**
KIND requires `kubectl` to interact with the Kubernetes cluster.

 Download and install `kubectl`:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

 Make it executable:
```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

 Verify installation:
```bash
kubectl version --client
```

---

## **Step 4: Install KIND**
 Download and install KIND:
```bash
curl -Lo ./kind "https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64"
```

 Make it executable:
```bash
chmod +x kind
sudo mv kind /usr/local/bin/
```

 Verify KIND installation:
```bash
kind version
```

---

## **Step 5: Create a KIND Cluster**
 Create a simple single-node cluster:
```bash
kind create cluster --name my-cluster
```

 Check if the cluster is running:
```bash
kubectl get nodes
```
Expected Output:
```
NAME                     STATUS   ROLES                  AGE     VERSION
my-cluster-control-plane   Ready    control-plane,master   1m      v1.XX.X
```

---

## **Step 6: Verify Cluster Setup**
 Check cluster status:
```bash
kind get clusters
```

 Get Kubernetes cluster info:
```bash
kubectl cluster-info
```

 Get running pods in all namespaces:
```bash
kubectl get pods -A
```

---

## **Step 8: Delete KIND Cluster (Optional)**
If you want to delete the cluster, run:
```bash
kind delete cluster --name my-cluster
```

---

## **Summary of Commands**
| **Step** | **Command** |
|----------|------------|
| Update system | `sudo apt update && sudo apt upgrade -y` |
| Install Docker | `sudo apt install -y docker.io` |
| Enable Docker | `sudo systemctl enable --now docker` |
| Check Docker version | `docker --version` |
| Install kubectl | `curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"` |
| Move kubectl to bin | `chmod +x kubectl && sudo mv kubectl /usr/local/bin/` |
| Verify kubectl | `kubectl version --client` |
| Install KIND | `curl -Lo ./kind "https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64"` |
| Move KIND to bin | `chmod +x kind && sudo mv kind /usr/local/bin/` |
| Verify KIND | `kind version` |
| Create KIND cluster | `kind create cluster --name my-cluster` |
| Check nodes | `kubectl get nodes` |
| Check cluster info | `kubectl cluster-info` |
| Delete KIND cluster | `kind delete cluster --name my-cluster` |

---
