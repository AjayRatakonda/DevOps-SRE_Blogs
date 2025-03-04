### **What is EFS in AWS?**  

**EFS (Elastic File System)** is a **shared storage service in AWS** that allows multiple EC2 instances to access the **same files** at the same time.  

### **Simple Explanation**  
- Think of **EFS as a shared folder** that multiple EC2 instances can use.  
- Unlike **EBS**, which is attached to only one instance, **EFS can be mounted on multiple instances**.  
- It **automatically scales** as data grows.  

### **Key Differences Between EBS and EFS**  
| Feature | EBS | EFS |
|---------|-----|-----|
| **Access** | One EC2 at a time | Multiple EC2 instances |
| **Scaling** | Must resize manually | Automatically scales |
| **Use Case** | Database storage | Shared storage for multiple servers |

---

## **How to Create EFS & Mount EFS to an EC2 Servers**  

### **Step 1: Create an EFS File System**
1. **Go to AWS Console** → **EFS Dashboard**.  
2. Click **"Create File System"**.  

  ![image](https://github.com/user-attachments/assets/52e68248-2fae-4ea6-81d6-a6166c5a3e97)

4. Choose the **VPC** where your EC2 instance is running(here my instance vpc id is vpc-07f39eca99ae0d016).  
5. Click **"Create"** and wait for the file system to be available.  

  ![image](https://github.com/user-attachments/assets/d182c272-4886-4641-85f3-596c486f3193)
  ![image](https://github.com/user-attachments/assets/37b80315-cbea-43ac-911a-e23af46965a3)

---

### **Step 2: Attach EFS to EC2 Instance**  
1. Go to **EFS Dashboard** → Select your EFS.  

  ![image](https://github.com/user-attachments/assets/be4913bb-34cc-4c6e-8395-15b06840f8f7)

2. Click **"Attach"** → Mount via DNS recommended, Copy the provided **mount command**.  

  ![image](https://github.com/user-attachments/assets/a7a0c809-4148-4691-96dc-3eddfac28fa0)
  ![image](https://github.com/user-attachments/assets/e6cf5400-6bdc-4e94-8f99-6f4ebe5aa076)
  
3. SSH into your EC2 instance:  
   ```sh
   ssh -i your-key.pem ec2-user@<instance-ip>
   ```
4. Install the **NFS client** (needed for EFS).  

   **For Amazon Linux / CentOS:**  
   ```sh
   sudo yum install -y amazon-efs-utils
   ```
   **For Ubuntu/Debian:**  
   ```sh
   sudo apt update
   sudo apt install -y nfs-common
   ```
   ![image](https://github.com/user-attachments/assets/c06b8bf6-8ad6-4ab3-8d2f-d2b8659f855f)

5. Create a mount directory:  
   ```sh
   sudo mkdir -p /mnt/efs
   ```
   
6. Mount the EFS file system:  
   ```sh
   sudo mount -t efs fs-0abe2a01a23cd589a:/ /mnt/efs
   ```
   Replace `fs-xxxxxxxx` with your **EFS File System ID**.

   if above command not works, use below command to mount the efs file system.
   ```sh
   sudo mount -t nfs4 fs-0abe2a01a23cd589a.efs.ap-south-1.amazonaws.com:/ /mnt/efs
   ```
   Replace `fs-xxxxxxxx` with your **EFS File System ID** like below. 

   ![image](https://github.com/user-attachments/assets/9fe74b94-fe66-478a-b348-297eab5708b0)
   
---

### **Step 3: Verify the Mount**  
Check if EFS is mounted:  
```sh
df -h
```
You should see an entry like:

![image](https://github.com/user-attachments/assets/ee093ac3-37cc-4359-b32a-7474dc785955)


---

### **Step 4: Make the Mount Permanent (Optional)**
To automatically mount EFS after reboot:  
1. Open the fstab file:  
   ```sh
   sudo nano /etc/fstab
   ```
2. Add this line at the end:
   ```
   fs-xxxxxxxx:/ /mnt/efs efs defaults,_netdev 0 0
   ```
3. Save and exit (**CTRL+X**, then **Y**, then **Enter**).  

Now, EFS will mount automatically when the instance reboots.  

---

## **Final Check**  
- Run **`ls /mnt/efs`** to list files.  
- Create a test file:  
  ```sh
  sudo touch /mnt/efs/testfile.txt
  ```
  ![image](https://github.com/user-attachments/assets/326385a2-5702-48e1-9b6d-182e6b2cb585)
  
- now create another ec2-instance with same vpc/same availability-zone
- connect to instance and use below commands
  **For Ubuntu/Debian:**
  ```sh
  sudo apt update
  sudo apt install -y nfs-common
  ```
  ![image](https://github.com/user-attachments/assets/c06b8bf6-8ad6-4ab3-8d2f-d2b8659f855f)
  
- Create a mount directory:  
   ```sh
   sudo mkdir -p /mnt/efs
   ```
- Mount the EFS file system:
  ```sh
  sudo mount -t nfs4 fs-0abe2a01a23cd589a.efs.ap-south-1.amazonaws.com:/ /mnt/efs
  ```
  Replace `fs-xxxxxxxx` with your **EFS File System ID**.  

  ![image](https://github.com/user-attachments/assets/9fe74b94-fe66-478a-b348-297eab5708b0)

- Check if another EC2 instance can see the file:  
  ```sh
  ls /mnt/efs
  ```
  ![image](https://github.com/user-attachments/assets/95845fa4-1fa9-444e-8565-fe72fdaa3ce1)

- now i create one another file in one instance it will automatically reflects two servers like below 
  ```sh
  touch ajay.txt
  ```
  ![image](https://github.com/user-attachments/assets/2e5af643-0e0d-499e-8faf-7ecc1036f510)

- now we will check another server also, whether same file is reflecting or not.

  ![image](https://github.com/user-attachments/assets/47e0193b-dc06-4058-a212-e4b2c68c43e3)

  see above screenshot the ajay.txt file is automatically reflected.
  
Now **both EC2 instances can share the same files** using EFS. 

---
