# **Spot Instances with Load Balancer**  

This guide explains how to create **Spot Instances** in AWS and attach them to an **Elastic Load Balancer (ELB)** using the **AWS Management Console**.  

---

## **Step 1: Create a Spot Fleet Request**  
1. In the **EC2 Dashboard**, select **Spot Requests** in the left panel.  
2. Click **"Request Spot Instances"**.  
3. Under **Launch Template**, click **"Create launch template"**.  
4. Provide a **Template Name** (e.g., `SpotInstanceTemplate`).  
5. Under **AMI (ubuntu Image)**.  
6. Under **Instance Type**, select **t2.micro** or any preferred type.  
7. Under **Key pair (login)**, choose an existing key pair or create a new one.  
8. In **Network settings**, select the **default VPC** and **at least two subnets**.  
9. Under **Security Group**, allow **HTTP (port 80)** and **SSH (port 22)**.  
10. Click **"Create launch template"**.

    ![image](https://github.com/user-attachments/assets/18c0ce07-74ab-4c41-a236-f0dcc7af9e00)
    ![image](https://github.com/user-attachments/assets/9f65ee00-0439-47fc-a16b-effcb3f05280)

---

## **Step 2: Create an Auto Scaling Group for Spot Instances**  
1. In the **EC2 Dashboard**, go to **Auto Scaling Groups**.  
2. Click **"Create Auto Scaling group"**.  
3. Enter a **name** (e.g., `SpotAutoScalingGroup`).  
4. Under **Launch Template**, select **SpotInstanceTemplate**.  
5. Click **"Next"**.  

  ![image](https://github.com/user-attachments/assets/a51ff733-1c13-4611-b88f-07512cd879da)

6. Under **Instance purchase options**, select **"Use Spot Instances"**.
7. Select an **existing VPC** and at least **two subnets**.  

  ![image](https://github.com/user-attachments/assets/683027c4-9e56-4d75-9616-ea0855349230)

## **Step 3: Create an Elastic Load Balancer (ELB)**  
1. Choose **"Attach to a new Load Balancer"**.
2. Under **Load Balancer Type**, choose **"Application Load Balancer"**. 
3. Enter a **name** (e.g., `SpotLoadBalancer`).        
4. Select an **existing VPC** and at least **two subnets**.     

   ![image](https://github.com/user-attachments/assets/19ceb6e6-9a5a-44aa-b35d-ff4e5d12787f)

5. Under **Target Group**, choose **"Create a new target group"**.  
6. Set **Target Type** as **"Instance"**.  
7. Provide a **Target Group Name** (e.g., `SpotTargetGroup`).

  ![image](https://github.com/user-attachments/assets/10604785-f141-49a7-9c6d-ad4ba1e0c2ce)
  ![image](https://github.com/user-attachments/assets/15adaee5-069f-4b9d-9182-50a11926a479)

## **Step 4: Configure group size and scaling** 
1. Set **Minimum: 1**, **Desired: 1**, **Maximum: 4**.  
2. Select **"Target Tracking Scaling Policy"**.  
3. Click **"Create a scaling policy"**.  
4. Set **Metric Type: Average CPU Utilization**.  
5. Set **Target Value: 50%**.  

  ![image](https://github.com/user-attachments/assets/74df4adb-e8a5-407b-aa83-b18ef5415f5d)

6. Click **"Next"**.      

---

## **Step 5: Review and Create the Auto Scaling Group**  
1. Review all the settings.  

   ![image](https://github.com/user-attachments/assets/b5de1717-de38-48c5-a9ba-1aab55b236ff)

2. Click **"Create Auto Scaling Group"**.  

  ![image](https://github.com/user-attachments/assets/0273c25c-f9ba-4e3b-b7cd-2c9006317442)

---

## **Step 6: Test Load Balancer and Spot Instances**  
1. Go to **EC2 Instances** and check if Spot Instances are running.  

  ![image](https://github.com/user-attachments/assets/21019c57-e476-4324-b62c-92c604d4586a)

3. Copy the **DNS name** of the Load Balancer from the **Load Balancer section**.  

  ![image](https://github.com/user-attachments/assets/ad70db7c-78d0-45f7-b4c7-33798ec8e93c)

4. Open a web browser and access:  
   ```
   http://<Load-Balancer-DNS>
   ```
5. If Auto Scaling is working, new Spot Instances will be launched as needed.  

---

## **Step 7: Clean Up Resources**  
1. In the **EC2 Dashboard**, go to **Auto Scaling Groups** and delete `SpotAutoScalingGroup`.  

    ![image](https://github.com/user-attachments/assets/0dbca856-6718-4abf-8a9e-aa67df69d800)

2. Go to **Load Balancers** and delete `SpotLoadBalancer`.  

   ![image](https://github.com/user-attachments/assets/d54a94e4-d490-4abb-a92e-ece8922d3c76)
 
3. Go to **Target Groups** and delete `SpotTargetGroup`.  

   ![image](https://github.com/user-attachments/assets/07a4a229-714d-4508-9d3e-44c21e45d885)

4. Go to **Spot Requests** and cancel active spot instances.  
5. Check **EC2 Instances** and terminate any running instances.  

---

## **Conclusion**
- Created **Spot Instances** using an **Auto Scaling Group**.  
- Attached them to an **Application Load Balancer**.  
- Configured **Auto Scaling policies** to handle traffic dynamically.  
- Tested the setup and cleaned up resources to avoid extra charges.  

---
