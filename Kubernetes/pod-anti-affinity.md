## **Pod Anti-Affinity Example (Prevent Pods from Running on the Same Node)**  
This Deployment ensures that **pods do not run on the same node**, helping distribute them across multiple nodes.

### **Create `deployment-pod-anti-affinity.yaml`**
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
