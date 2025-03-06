### **What is AWS FIS (Fault Injection Simulator)?**  
- AWS Fault Injection Simulator (**AWS FIS**) is a **chaos engineering service** that **helps test how applications respond to failures**.
- It allows you to **simulate real-world failures** like instance crashes, high CPU usage, network delays, or database failures to **improve system resilience and reliability**.  

---

### **How to Practice AWS FIS (Step by Step Guide)**  

#### **Step 1: Sign in to AWS Console**  
- Go to the **AWS Management Console**.
  
**1. Launch a Spot Instance**

   - Open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/).
   - Click **"Launch Instances"**.
   - Configure your instance as needed.
   - at the time of creating instance install SSM using below script(here my os is ubuntu)

     ![image](https://github.com/user-attachments/assets/25a2bc78-b6c8-4e21-a8bd-ad84c2cd43fd)
     ![image](https://github.com/user-attachments/assets/2bfa56e1-b1ce-4867-a2fd-1b7f4f160e55)

   - and connect with ec2 and check wheter SSM installed or not.

     ![image](https://github.com/user-attachments/assets/e9b3d006-f0f7-47d1-af09-3432d9b62efd)

   - and check cpu usage like below.

     ![image](https://github.com/user-attachments/assets/7baea46b-bb22-496b-9260-7914d9dc534b)

   - Complete the launch process.

**2. Create IAM Role**

    - create one role and give policy as administrative access like below.

    ![image](https://github.com/user-attachments/assets/5640869a-6bb8-4d7f-9d0f-4462a44b10aa)

     
**3. Navigate to AWS FIS service**

    - click on scenario library and select EC2 Stress:CPU and click on create template with scenario option like below screenshot.

      ![image](https://github.com/user-attachments/assets/1f6af4ac-6f04-48e3-83b0-2f6e456ad7ae)
      ![image](https://github.com/user-attachments/assets/716191c8-05ea-4079-9ed7-8d1d787582a4)  
      ![image](https://github.com/user-attachments/assets/b4c0870e-123f-4b8a-ace0-94aa36c028e0)

    - create new role and add policy as like below

      ![image](https://github.com/user-attachments/assets/1fb15edd-0bd1-438b-9afb-523d69e66f53)

      ![image](https://github.com/user-attachments/assets/e5d2c231-919c-4b3d-bb76-e9f5b6ad8db6)
      ![image](https://github.com/user-attachments/assets/8b1ad2ee-8209-4b21-a0e8-6bbbbb97b380)

    - click on create experiment 

**4. Start the Experiment**

   - In the AWS FIS console, navigate to **"Experiment templates"**.
   - Select your template and click **"Actions"**, then **"Start experiment"**.
   - Confirm to initiate the experiment.

     ![image](https://github.com/user-attachments/assets/6d35812a-ca26-499a-a449-7227f8a6489b)
     ![image](https://github.com/user-attachments/assets/d95c0e70-e43b-4b77-ae85-042841ac76ea)


**5. Monitor the Experiment**

   - Monitor the experiment's progress in the AWS FIS console.
   - After the specified duration, AWS FIS will simulate a Spot Instance interruption.
   - Ensure your application responds appropriately to the interruption.

**6. Clean Up Resources**

   - Terminate the Spot Instance if it's no longer needed.
   - Delete the AWS FIS experiment template if not required for future tests.

For more detailed information, refer to the [AWS FIS User Guide on testing Spot Instance interruptions](https://docs.aws.amazon.com/fis/latest/userguide/fis-tutorial-spot-interruptions.html).

---
