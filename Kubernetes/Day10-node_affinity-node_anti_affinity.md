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

## **2️. Node Anti-Affinity (Pods must avoid specific nodes)**
### **Step 1: Label a Node to Avoid**
```sh
kubectl label node <node-name> env=restricted
```
Verify the label:
```sh
kubectl get nodes --show-labels
```

### **Step 2: Create a Deployment with Node Anti-Affinity**
Save the following as **`node-anti-affinity.yaml`**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 2
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
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: env
                    operator: NotIn
                    values:
                      - restricted
      containers:
        - name: nginx
          image: nginx
```

### **Step 3: Apply the Deployment**
```sh
kubectl apply -f node-anti-affinity.yaml
```

### **Step 4: Verify Pod Placement**
```sh
kubectl get pods -o wide
```
- Pods **will not be scheduled** on nodes labeled **`env=restricted`**.

---

### **Remove a Label from a Node**
```sh
kubectl label node <node-name> env-
```

### **Delete the Deployments**
```sh
kubectl delete deployment my-deployment
```

---

### **Summary**
| **Feature**         | **Node Affinity** | **Node Anti-Affinity** |
|--------------------|------------------|----------------------|
| **Purpose**       | Schedule pods **on specific nodes** | Spread pods across multiple nodes |
| **Use Case**      | Run database pods on high-memory nodes | Prevent all replicas from running on the same node |
| **Hard Rule**     | `requiredDuringSchedulingIgnoredDuringExecution` | `requiredDuringSchedulingIgnoredDuringExecution` |
| **Soft Rule**     | `preferredDuringSchedulingIgnoredDuringExecution` | `preferredDuringSchedulingIgnoredDuringExecution` |

This is a **simple way to use Node Affinity & Anti-Affinity** in Kubernetes Deployments.
