# **Amazon EKS Installation and Spot Instance Auto Scaling Setup on AWS CloudShell**  

This document provides a step-by-step guide to installing and configuring Amazon Elastic Kubernetes Service (EKS) on AWS CloudShell. It includes instructions to install `eksctl`, configure AWS credentials, deploy an EKS cluster with Spot instances and auto-scaling, and delete the cluster when no longer needed.  

---

## **1. Install `eksctl` on AWS CloudShell**  

### **Step 1: Determine System Architecture**  
AWS CloudShell runs on Amazon Linux, which is typically `x86_64` (amd64). If using an ARM-based system, set the appropriate architecture.  

```sh
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
```

### **Step 2: Download `eksctl`**  
```sh
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
```

### **Step 3: Verify the Download (Optional)**
```sh
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
```

### **Step 4: Extract and Install `eksctl`**
```sh
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo mv /tmp/eksctl /usr/local/bin
```

### **Step 5: Verify Installation**
```sh
eksctl version
```

---

## **2. Configure AWS Credentials**  

To authenticate AWS CLI and `eksctl`, configure AWS credentials.  

```sh
aws configure
```
It will prompt for:  
- **AWS Access Key ID**  
- **AWS Secret Access Key**  
- **Default Region (ap-south-1 for Mumbai, or another preferred region)**  
- **Output format (leave as default: `json`)**  

---

## **3. Create an EKS Cluster with Spot Instances and Auto Scaling**  

The following command will create an EKS cluster named `my-eks-cluster` in **ap-south-1** (Mumbai) with:  
- One **Spot instance** (`t3.medium`)  
- **Auto Scaling** from 1 to 3 nodes  
- A **managed node group**  

```sh
eksctl create cluster --name my-eks-cluster \
  --region ap-south-1 \
  --version 1.27 \
  --nodegroup-name spot-nodes \
  --node-type t3.medium \
  --spot \
  --nodes 1 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

### **Verify the Cluster**
Check the EKS cluster status:  
```sh
eksctl get cluster --region ap-south-1
```

Check if nodes are running:  
```sh
kubectl get nodes
```

Check the Auto Scaling Group:  
```sh
aws autoscaling describe-auto-scaling-groups --region ap-south-1
```

---

## **4. Deploy a Sample Application**  

To test if the EKS cluster is working, deploy a sample application.  

### **Create a Deployment YAML File**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: test-app
  template:
    metadata:
      labels:
        app: test-app
    spec:
      containers:
      - name: nginx
        image: nginx
```

### **Apply the Deployment**
```sh
kubectl apply -f deployment.yaml
```

### **Check the Running Pods**
```sh
kubectl get pods -o wide
```

---

## **5. Delete the EKS Cluster**  

To delete the cluster and associated resources:  

```sh
eksctl delete cluster --name my-eks-cluster --region ap-south-1
```

This will remove:  
- The EKS Cluster  
- The Managed Node Group (Spot Instances)  
- The Auto Scaling Group  

To verify that the cluster is deleted, run:  
```sh
eksctl get cluster --region ap-south-1
```
It should return:  
```
No clusters found
```

---

## **Summary of Steps**
1. **Install `eksctl` on AWS CloudShell**  
2. **Configure AWS CLI with `aws configure`**  
3. **Create an EKS cluster with Spot instances and Auto Scaling**  
4. **Verify cluster and deploy a sample application**  
5. **Delete the EKS cluster**  
6. **Manually clean up remaining AWS resources (if needed)**  

---
