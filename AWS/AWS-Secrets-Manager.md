### **What is AWS Secrets Manager?**  
AWS Secrets Manager is a service that helps you **store, manage, and retrieve sensitive information** (like passwords, API keys, database credentials) securely. It automatically **rotates secrets**, reducing the risk of exposure.  

---

### **How to Practice AWS Secrets Manager with K3s?**  

You can **store a secret** in AWS Secrets Manager and use it in K3s. Follow these steps:  

#### **Step 1: Create a Secret in AWS Secrets Manager**  
1. **Go to AWS Console** → **Secrets Manager**.  

  ![image](https://github.com/user-attachments/assets/bf500b49-96e2-44f3-89a4-1ea257ec3ccc)

3. Click **Store a new secret**.  

  ![image](https://github.com/user-attachments/assets/e528718d-c8c8-4d55-80d9-a7e9fc82d5fb)

5. Choose **Other type of secret**.  

6. Enter your secret key-value pair. Example:  
   - Key: `DB_PASSWORD`  
   - Value: `mypassword123`  

  ![image](https://github.com/user-attachments/assets/caad49b2-ebff-4d5d-8200-47ba7f63475c)

8. Click **Next** and give the secret a name (e.g., `my-db-secret`).  
  
  ![image](https://github.com/user-attachments/assets/1cba4d48-9822-4143-bd92-88f25481cfed)

9. Click **Store**.  

  ![image](https://github.com/user-attachments/assets/7fd7c3d5-e0ba-4b2b-b3da-bcc345855663)
  ![image](https://github.com/user-attachments/assets/de1a8e79-31d2-4122-bf3c-3824cb442e8c)
  ![image](https://github.com/user-attachments/assets/1630f16b-16fa-4474-98fd-f46823439b68)

- create one instance like below.

  ![image](https://github.com/user-attachments/assets/65df9f29-ae1a-491c-995d-169bbf647047)

- connect to instance and install k3s on that server like below.

  ![image](https://github.com/user-attachments/assets/ed62e900-dca4-4a5e-9e3b-0e3f5b86a001)

  ```bash
  sudo apt update && sudo apt upgrade -y
  curl -sfL https://get.k3s.io | sh -
  ```
  
  verify k3s installation:

  ```bash
  kubectl version
  kubectl get nodes
  ```
  ![image](https://github.com/user-attachments/assets/a62e445e-315d-431b-b7e1-3e1317c9802c)

---


#### **Step 2: Create an IAM Role for K3s**  
1. Go to **IAM** → **Roles** → **Create role**.  

  ![image](https://github.com/user-attachments/assets/adb6d312-083d-44a5-9594-9d5b3c306af4)
  
2. Select **EC2** (if running K3s on EC2).  

  ![image](https://github.com/user-attachments/assets/d247f7ff-f0b3-4233-8aaf-90f9c693c7d0)
  ![image](https://github.com/user-attachments/assets/42db1b8b-a115-4698-966d-bca6fa6a3b69)
  
3. Attach the **SecretsManagerReadWrite** policy.  

  ![image](https://github.com/user-attachments/assets/d0e1d5ce-74cf-4d8f-a126-c98328d012bd)
  ![image](https://github.com/user-attachments/assets/c604b49e-8d00-41ef-8b23-eed45c9c95f8)
  ![image](https://github.com/user-attachments/assets/56a7e050-5ddb-4aed-b4e1-073d03e92ded)
  ![image](https://github.com/user-attachments/assets/febdd87e-e470-46b1-a546-051b5d6108a2)

4. Create the role and attach it to your K3s instance.  

  ![image](https://github.com/user-attachments/assets/bd2744e8-3668-4226-83e0-1701d88565c2)
  ![image](https://github.com/user-attachments/assets/33efed86-7e45-40cb-997f-b45b09839f1d)
  ![image](https://github.com/user-attachments/assets/c1468b17-a09a-46c9-881f-8f7266a15421)
  ![image](https://github.com/user-attachments/assets/582f32f7-f4e8-460b-89fa-42bdb5cffcae)

---

#### **Step 3: Access Secret from K3s Pod**  
1. **Install AWS CLI** inside K3s instance:  
   ```bash
   sudo apt update && sudo apt install awscli -y
   ```
  ![image](https://github.com/user-attachments/assets/fbbba352-a416-412d-b6e1-f1f1d11c46de)

2. **Get the secret using AWS CLI**:  
   ```bash
   aws secretsmanager get-secret-value --secret-id my-db-secret --region ap-south-1
   ```
   ![image](https://github.com/user-attachments/assets/746842c6-2483-4322-a2ab-2144cc184c59)

3. **Use it in Kubernetes Secrets**:  
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: my-db-secret
   type: Opaque
   data:
     DB_PASSWORD: bXlwYXNzd29yZDEyMw==  # Base64 encoded value
   ```
   ![image](https://github.com/user-attachments/assets/f5c54126-6f9b-4acd-b5dd-8fa7c49fa9e2)

   ```sh
   kubectl apply -f secrets.yaml
   ```
   
   ![image](https://github.com/user-attachments/assets/0ec03e31-9057-4773-820f-55ab83e8226c)

      
4. **Mount it in a Deployment**:  
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-app
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
           - name: my-container
             image: nginx
             env:
               - name: DB_PASSWORD
                 valueFrom:
                   secretKeyRef:
                     name: my-db-secret
                     key: DB_PASSWORD
   ```
   
5. Apply the manifest:  
   ```bash
   kubectl apply -f deployment.yaml
   ```
   ![image](https://github.com/user-attachments/assets/cade4a8c-473d-4972-83c6-74f323db912f)
   ![image](https://github.com/user-attachments/assets/22ff1944-f81f-48f5-b136-dcc904734472)

   
---

Now, your K3s app can access the **AWS secret** securely without hardcoding credentials. 
