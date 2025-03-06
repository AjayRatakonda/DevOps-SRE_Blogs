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
   - In the **"Instance Purchasing Options"** section, select **"Request Spot Instances"**.
   - Complete the launch process.

**2. Create an IAM Role for AWS FIS**

   - Open the [IAM console](https://console.aws.amazon.com/iam/).
   - Click **"Roles"**, then **"Create role"**.
   - Choose **"AWS service"** and select **"Fault Injection Simulator"**.

     ![Screenshot 2025-03-06 173643](https://github.com/user-attachments/assets/a550fbb6-d0ae-43bc-8b94-b6537acfa9f2)
    
   - Attach the **"AWSFaultInjectionSimulatorFullAccess"** policy.

     ![Screenshot 2025-03-06 173652](https://github.com/user-attachments/assets/5273a5b9-4cbf-45f2-943e-cf81b253d4c9)

   - Name the role (e.g., **"FIS-Spot-Interruption-Role"**) and create it.

     ![Screenshot 2025-03-06 173832](https://github.com/user-attachments/assets/09cbc05d-b491-4134-8d3d-c937799734d3)
     ![image](https://github.com/user-attachments/assets/18339afa-3c73-4595-ae3f-f1d57db3e549)

     
**3. Create an AWS FIS Experiment Template**

   - Open the [AWS FIS console](https://console.aws.amazon.com/fis/).

     ![Screenshot 2025-03-06 160022](https://github.com/user-attachments/assets/d458bd53-a55f-4c94-9eab-b0d026069b4a)

   - Click **"Experiment templates"**, then **"Create experiment template"**.

     ![Screenshot 2025-03-06 160233](https://github.com/user-attachments/assets/5660d4f6-097d-442b-a85b-56c91eee5a2a)

   - Provide a name and description.

     ![image](https://github.com/user-attachments/assets/54d67518-c955-4dff-948d-2083040d0494)
     
   - Under **"Actions"**, click **"Add action"**:
     - Action type: **"aws:ec2:send-spot-instance-interruptions"**.
     - Parameters: Set **"durationBeforeInterruption"** (e.g., **"PT2M"** for 2 minutes).

       ![image](https://github.com/user-attachments/assets/b137fc77-ac88-4e10-a5f6-bd0332a88414)

   - Under **"Targets"**, click **"Add target"**:
     - Resource type: **"aws:ec2:instance"**.
     - Selection mode: **"ALL"** or specify criteria to target your Spot Instance.

       ![image](https://github.com/user-attachments/assets/8da6ae77-f770-45aa-9204-73d6801b1d9d)

   - For **"Role"**, select the IAM role created earlier.

     ![image](https://github.com/user-attachments/assets/ed30aeb1-d40e-4c17-a8c8-d7d13e35e7f6)

   - Review and create the template.

     ![image](https://github.com/user-attachments/assets/f37ce7fd-3560-4ef1-87ab-57ce1c4db801)
     ![image](https://github.com/user-attachments/assets/b247ca6a-6c45-4753-9390-20dd0c81389f)
     ![image](https://github.com/user-attachments/assets/3893f736-ffcc-4c89-b015-af2c3a89333c)

**4. Start the Experiment**

   - In the AWS FIS console, navigate to **"Experiment templates"**.
   - Select your template and click **"Actions"**, then **"Start experiment"**.
   - Confirm to initiate the experiment.

     ![image](https://github.com/user-attachments/assets/a4ce6a2c-34d1-4a09-8b0c-a12aa6713b47)
     ![image](https://github.com/user-attachments/assets/7404a5a3-3c65-497d-9671-91035d7c100c)

**5. Monitor the Experiment**

   - Monitor the experiment's progress in the AWS FIS console.
   - After the specified duration, AWS FIS will simulate a Spot Instance interruption.
   - Ensure your application responds appropriately to the interruption.

**6. Clean Up Resources**

   - Terminate the Spot Instance if it's no longer needed.
   - Delete the AWS FIS experiment template if not required for future tests.

For more detailed information, refer to the [AWS FIS User Guide on testing Spot Instance interruptions](https://docs.aws.amazon.com/fis/latest/userguide/fis-tutorial-spot-interruptions.html).

---
