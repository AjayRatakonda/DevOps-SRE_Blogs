### **What is Kubernetes HPA (Horizontal Pod Autoscaler)?**  

Kubernetes **Horizontal Pod Autoscaler (HPA)** automatically **scales the number of pods** in a **Deployment, ReplicaSet, or StatefulSet** based on **CPU or memory usage**.  

- If CPU/memory usage is **high**, HPA **adds more pods**.  
- If CPU/memory usage is **low**, HPA **removes extra pods**.  

---

## **Step-by-Step Demo: HPA in Kubernetes**
### **Step 1: Enable Metrics Server**  
HPA requires the **Metrics Server** to check resource usage.  

Deploy the Metrics Server:  
```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
**Sample Output**

![image](https://github.com/user-attachments/assets/fafd6cf1-b968-4517-b997-9ec3fbb12018)

Verify that Metrics Server is running:
```sh
kubectl get deployment -n kube-system metrics-server
```
**Sample Output**

![image](https://github.com/user-attachments/assets/01f712fc-ca24-4f7f-92b8-c868a5f611b0)

If it’s not running, check logs:
```sh
kubectl logs -n kube-system deployment/metrics-server
```

---

### **Step 2: Create a Deployment (`deployment.yaml`)**
Create a simple **nginx Deployment** with **CPU requests and limits**.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: nginx-container
          image: nginx
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: "100m"  # Requests 0.1 CPU
            limits:
              cpu: "200m"  # Max 0.2 CPU
```
Apply the Deployment:
```sh
kubectl apply -f deployment.yaml
```
**Sample Output**

![image](https://github.com/user-attachments/assets/9b8c2825-bd50-4ac1-be01-ddbc879f5063)

Check deployment:
```sh
kubectl get deployment
```
**Sample Output**

![image](https://github.com/user-attachments/assets/72f8ff8e-bbf1-421d-8915-a9f5cd29fb23)

service.yaml:
```sh
apiVersion: v1
kind: Service
metadata:
  name: my-deployment
spec:
  selector:
    app: my-app  # Matches the deployment labels
  ports:
    - protocol: TCP
      port: 80        # The service will expose this port
      targetPort: 80  # The container inside the pod listens on this port
  type: ClusterIP
```
**Sample Output**

![image](https://github.com/user-attachments/assets/79354ff7-f50d-4a75-9c1c-8e05889cc5fa)

---

### **Step 3: Create an HPA (`hpa.yaml`)**
Create an **HPA resource** that scales pods **between 1 and 7 replicas**, based on **CPU usage above 50%**.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 7
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50  # Scale up if CPU > 50%
```
Apply the HPA:
```sh
kubectl apply -f hpa.yaml
```
or we can create hpa by using below command:
```sh
kubectl autoscale deployment my-deployment --min=1 --max=7 --cpu-percent=50
```
**Sample Output**

![image](https://github.com/user-attachments/assets/9a6bfb00-8a51-4c67-aa53-c90abe3376a1)

Check the HPA:
```sh
kubectl get hpa
```
**Sample Output**

![image](https://github.com/user-attachments/assets/cf854e34-e3e0-436e-b15b-40b1ab1cde3c)

- **TARGETS 0%/50%** → Current CPU usage is **0%**, but HPA will **scale when it reaches 50%**.

---

### **Step 4: Simulate High CPU Load**
HPA only works when CPU usage is **above the defined threshold** (50% in this case).  

Run the following command to **increase CPU load**:
```sh
kubectl run -it --rm --image=busybox load-generator -- /bin/sh -c "while true; do wget -qO- http://my-deployment.default.svc.cluster.local; done"
```
Check the HPA again:
```sh
kubectl get hpa
```
If the CPU usage is high, **HPA will automatically increase pods**.

**Sample Output**

![image](https://github.com/user-attachments/assets/527a7bb5-1674-4453-b51e-77c7406183b7)

Check the pods:
```sh
kubectl get pods
```
**Sample Output**

![image](https://github.com/user-attachments/assets/544a03ee-5898-480a-a1f8-1da274f2528f)

Now, there are **2 pods running** instead of **1**.

---

### **Step 5: Stop Load & Scale Down**
Stop the load test by pressing **CTRL+C**.

Check the HPA after some time:
```sh
kubectl get hpa
```
Once CPU usage decreases, **HPA will automatically scale down** the pods.
**Sample Output**

![image](https://github.com/user-attachments/assets/d3b655f6-e3b6-4ba6-8d2c-d282518f12f0)

---

## **Summary**
1. **HPA monitors CPU/memory usage** and adjusts the number of pods automatically.
2. **Minimum and maximum replicas** are defined in HPA.
3. **If CPU > 50%**, HPA **adds more pods**.
4. **If CPU < 50%**, HPA **removes extra pods**.

This setup ensures your application **scales automatically based on demand**.

---
