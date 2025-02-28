### **Node Affinity & Node Anti-Affinity in Kubernetes (with Deployment Examples)**  
  
- **Node Affinity** allows pods to be scheduled **only on specific nodes** based on labels.  
- **Node Anti-Affinity** makes sure that pods do not run on the same node, so they get spread across different nodes.  

---

## **1. Node Affinity Example (Pods must run on specific nodes)**  
This Deployment ensures that pods **only run on nodes labeled `env=production`**.

### **Step 1: Label a Node**
```sh
kubectl label node worker-node-1 env=production
```
**Sample Output**

![image](https://github.com/user-attachments/assets/9281e769-65c9-44a1-9a55-f5da081c6e59)

### **Step 2: Create `deployment-node-affinity.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:  # Must match (Hard Rule)
            nodeSelectorTerms:
              - matchExpressions:
                  - key: env
                    operator: In
                    values:
                      - production
      containers:
        - name: nginx
          image: nginx
```

### **Explanation**
- **Node Affinity** ensures pods **only run** on nodes with `env=production`.  
- If no such node exists, the pod **remains in Pending state**.  

### **Step 3: Apply the Deployment**
```sh
kubectl apply -f deployment-node-affinity.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/6cb81a18-d757-4b1f-a545-866c68acbf4b)

### **Verify if pods are running on the correct nodes**
```sh
kubectl get pods -o wide
```
**Sample Output**

![image](https://github.com/user-attachments/assets/12fc35fe-e32a-473d-906f-d3c2cd993e27)

---

## **2. Node Anti-Affinity Example (Prevent Pods from Running on the Same Node)**  
This Deployment ensures that **pods do not run on the same node**, helping distribute them across multiple nodes.

### **Create `deployment-node-anti-affinity.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:  # Hard Rule (Must be on separate nodes)
            - labelSelector:
                matchLabels:
                  app: my-app
              topologyKey: "kubernetes.io/hostname"
      containers:
        - name: nginx
          image: nginx
```

### **Explanation**
- **Pod Anti-Affinity** ensures that **no two pods** with the label `app: my-app` run on the same node.  
- Uses `topologyKey: "kubernetes.io/hostname"`, meaning pods will be scheduled **on different nodes**.  
- If there are **not enough nodes**, some pods may **stay in Pending state**.  

### **Apply the Deployment**
```sh
kubectl apply -f deployment-node-anti-affinity.yaml
```

**Sample Output**

![image](https://github.com/user-attachments/assets/716c74b3-b0bd-48f1-a197-597e59c6932b)

### **Verify pod distribution**
```sh
kubectl get pods -o wide
```
Each pod should be running on **a different node**.

**Sample Output**

![image](https://github.com/user-attachments/assets/7e0d34fc-242c-4280-a32c-f9f5a962cdef)

---

### **Summary**
| **Feature**         | **Node Affinity** | **Node Anti-Affinity** |
|--------------------|------------------|----------------------|
| **Purpose**       | Schedule pods **on specific nodes** | Spread pods across multiple nodes |
| **Use Case**      | Run database pods on high-memory nodes | Prevent all replicas from running on the same node |
| **Hard Rule**     | `requiredDuringSchedulingIgnoredDuringExecution` | `requiredDuringSchedulingIgnoredDuringExecution` |
| **Soft Rule**     | `preferredDuringSchedulingIgnoredDuringExecution` | `preferredDuringSchedulingIgnoredDuringExecution` |

This is a **simple way to use Node Affinity & Anti-Affinity** in Kubernetes Deployments.
