# **Pod Anti-Affinity in Kubernetes (with Deployment Example)**  
- Pod Anti-Affinity ensures similer pods do not run on a same node, so they get spread across multiple nodes.

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

---

# **Pod Affinity in Kubernetes (with Deployment Example)**  

## **What is Pod Affinity?**  
Pod Affinity ensures that **pods prefer to be scheduled on the same node or close to other specific pods**. It is useful when:  
- Pods need **low latency communication** (e.g., web and database running together).  
- Certain workloads should **stay together for performance** reasons.  

---

## **Types of Pod Affinity**  
1. **Required (`requiredDuringSchedulingIgnoredDuringExecution`)**  
   - Pods **must be scheduled** on nodes where specific pods are already running.  
   - If no suitable node is found, the pod will remain in **Pending state**.  
   
2. **Preferred (`preferredDuringSchedulingIgnoredDuringExecution`)**  
   - Pods **prefer** to be scheduled on the same node but can run elsewhere if needed.  
   - Kubernetes **tries** to place them together but does not enforce it strictly.  

---

## **Example: Pod Affinity Deployment**
This example ensures that the **new pod will be scheduled on the same node as another pod with `app=frontend`**.

### **Step 1: Create a Deployment with Pod Affinity**
Save the following as **`pod-affinity.yaml`**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchLabels:
                  app: frontend
              topologyKey: "kubernetes.io/hostname"
      containers:
        - name: backend-container
          image: nginx
```

### **Explanation**
- **`podAffinity`**: Ensures that pods in this deployment are scheduled on the **same node as pods labeled `app=frontend`**.  
- **`topologyKey: "kubernetes.io/hostname"`**: Pods must be placed on the **same node**.  
- **If no node has `app=frontend` pods**, the backend pods **will not be scheduled**.  

---

## **Step 2: Apply the Deployment**
```sh
kubectl apply -f pod-affinity.yaml
```

---

## **Step 3: Verify Pod Placement**
Check where the pods are running:
```sh
kubectl get pods -o wide
```
- Backend pods should be scheduled on **the same node as frontend pods**.  
- If no frontend pods exist, backend pods will remain **Pending**.  

---

## **Preferred Pod Affinity Example (Pods Prefer Running Together but Not Mandatory)**  
This version **prefers** to place pods on the same node but does not enforce it strictly.

### **`pod-affinity-preferred.yaml`**
```yaml
affinity:
  podAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector:
            matchLabels:
              app: frontend
          topologyKey: "kubernetes.io/hostname"
```
- **`weight: 1`** → Determines the priority (higher weight = stronger preference).  
- **Pods will prefer to run on the same node as `app=frontend` but can be scheduled elsewhere if necessary**.  

---

## **Removing Pod Affinity**
If a pod is stuck in **Pending state** due to strict affinity rules, remove it by editing the deployment and deleting `affinity`.

---

## **Summary: Pod Affinity vs. Pod Anti-Affinity**  

| **Feature**          | **Pod Affinity**                                  | **Pod Anti-Affinity**                              |
|----------------------|--------------------------------------------------|--------------------------------------------------|
| **Purpose**         | Ensures pods run together on the same node.      | Ensures pods do not run on the same node.       |
| **Use Case**        | Web & database pods needing low-latency communication. | Spreading replicas of the same application across nodes for high availability. |
| **Hard Rule**       | `requiredDuringSchedulingIgnoredDuringExecution` | `requiredDuringSchedulingIgnoredDuringExecution` |
| **Soft Rule**       | `preferredDuringSchedulingIgnoredDuringExecution` | `preferredDuringSchedulingIgnoredDuringExecution` |
| **Risk**            | If no suitable node exists, pods remain **Pending**. | If not enough nodes are available, some pods may not be scheduled. |

Pod Affinity is used when pods need to run close to each other, while Pod Anti-Affinity is used to distribute pods across multiple nodes to prevent failures affecting all replicas.

---
