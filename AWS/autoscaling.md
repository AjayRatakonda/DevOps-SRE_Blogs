# **What is Auto scaling and How to set up Auto scaling in AWS**
### **What is Auto Scaling?**  
Auto Scaling is a feature that **automatically adds or removes resources** (like servers or containers) based on demand.  

### **Why is Auto Scaling Important?**  
- **Saves Cost** → Increases resources when needed, reduces them when idle.  
- **Improves Performance** → Ensures enough capacity to handle traffic.  
- **Handles Failures** → Replaces failed instances automatically.  

### **Types of Auto Scaling**
1. **EC2 Auto Scaling (AWS)** → Adjusts the number of EC2 instances.  
2. **Kubernetes HPA (Horizontal Pod Autoscaler)** → Increases or decreases pods in a cluster.  
3. **Application Auto Scaling** → Scales services like DynamoDB, ECS, etc.  

### **Example of Auto Scaling**
- **E-commerce website during a sale** → More users visit the site, so Auto Scaling **adds more servers**.  
- **Late at night** → Fewer users, so Auto Scaling **removes extra servers** to save costs.  

## Steps to set up auto scaling
## step 1: Launch an EC2 Instance for Auto Scaling
- In the EC2 Dashboard, click on Auto Scaling Groups
  
  ![image](https://github.com/user-attachments/assets/9bf307d8-b7ee-478c-991c-ede4678ae03a)

- provide auto scaling group name as "MyAutoScalingTemplate"
- click on "Create Launch Template" under Auto Scaling.

  ![image](https://github.com/user-attachments/assets/05fe6448-c580-408b-81df-d67742dc8deb)

- Enter a Launch Template Name (e.g., MyAutoScalingTemplate).

  ![image](https://github.com/user-attachments/assets/5c03ea6f-7b42-43f5-8621-b20f5088f6ec)

- Under AMI (Amazon Machine Image), choose Amazon Linux 2 (or any OS of your choice).
- Under Instance type, select a free-tier eligible instance like t2.micro.
- Under Key pair (login), choose an existing key pair(i have one keypair so i selected existing keypair) or create a new one.
- In the Network settings, keep the default VPC and subnet.

  ![image](https://github.com/user-attachments/assets/4d0efc5c-61ba-4251-a1ec-a5a41d4cb573)

- Under Security Group, allow HTTP (port 80) and SSH (port 22).
- Click "Create launch template".

  ![image](https://github.com/user-attachments/assets/dc9c13d3-3c62-41a4-a63a-1681cc72f4dd)

## Step 2: Create an Auto Scaling Group
- In the EC2 Dashboard, click "Auto Scaling Groups" on the left panel.
- Click "Create Auto Scaling group".
- Enter a name for the Auto Scaling group (e.g., MyAutoScalingGroup).
- Under Launch Template, select the one you created (MyAutoScalingTemplate).

  ![image](https://github.com/user-attachments/assets/b4344182-f0ab-4291-a97b-7d477b500bb6)

- Click "Next"

## Step 4: Configure Network and Subnets
- Select an existing VPC (default VPC is fine).
- Select at least two subnets in different Availability Zones.
- Click "Next".

  ![image](https://github.com/user-attachments/assets/cb2427ee-b1dd-4de1-a9b2-56883359c4f8)

## Step 5: Configure Auto Scaling Policies
- Under Desired capacity, set:
     Minimum instances: 1
     Desired instances: 2
     Maximum instances: 3
- Choose "Target Tracking Scaling Policy".
- Click "Create a scaling policy" and configure:
    Metric type: Average CPU Utilization
    Target value: 50%
    Cooldown period: 60 seconds 
- Click "Next".

  ![image](https://github.com/user-attachments/assets/3cb586f8-e5d8-4591-a83d-03a26450811d)
  ![image](https://github.com/user-attachments/assets/d162ec3a-279f-4282-b2cf-09d6e4a9e9a5)

  

  


