### **What is AWS FIS (Fault Injection Simulator)?**  
- AWS Fault Injection Simulator (**AWS FIS**) is a **chaos engineering service** that **helps test how applications respond to failures**.
- It allows you to **simulate real-world failures** like instance crashes, high CPU usage, network delays, or database failures to **improve system resilience and reliability**.  

---

### **How to Practice AWS FIS (Step by Step Guide)**  

#### **Step 1: Sign in to AWS Console**  
- Go to the **AWS Management Console**.  
- Search for **"Fault Injection Simulator"** in the search bar.  
- Click on **AWS Fault Injection Simulator**.  

#### **Step 2: Create an Experiment Template**  
- Click **"Create experiment template"**.  
- Give it a **name** (e.g., `Test-FIS-Experiment`).  
- Select an **IAM role** with **FIS permissions** (create one if needed).  

#### **Step 3: Define Target Resources**  
- Choose the **AWS service to test** (e.g., EC2, RDS, ECS).  
- Use **resource tags** to target specific instances (`Key: environment, Value: test`).  

#### **Step 4: Add Actions (Fault Injection Scenarios)**  
- Click **"Add action"** and choose a failure type:  
  - **Stop EC2 instances** (to test instance failure).  
  - **Increase CPU load** (to test performance under stress).  
  - **Network latency** (to check response to slow connections).  

#### **Step 5: Set Safety Controls**  
- Add a **stop condition** (e.g., CloudWatch alarm triggers if CPU > 90%).  
- Enable **rollback actions** to restore services after testing.  

#### **Step 6: Start Experiment**  
- Click **"Start experiment"**.  
- Monitor in **CloudWatch Logs**.  

#### **Step 7: Analyze & Improve**  
- Check **how your application behaved** during failure.  
- Adjust **auto-scaling, monitoring, or failover strategies** based on results.  

---

### **Use Case Example**  
Imagine you manage a web app on **EC2** with an **RDS database**. You can use AWS FIS to:  
- **Stop an EC2 instance** and check if the **Auto Scaling Group launches a new one**.  
- **Simulate high CPU usage** to see if **CloudWatch alarms trigger scaling**.  
- **Slow down network traffic** to check **how APIs respond to latency**.  

---
