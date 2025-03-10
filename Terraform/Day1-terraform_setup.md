# **What is Terraform?**  
Terraform is an **Infrastructure as Code (IaC)** tool that helps you create and manage cloud resources using code. It allows you to define infrastructure (like EC2 instances, VPCs, S3 buckets) in a configuration file and apply it to AWS or other cloud providers.  

---
### **How to Set Up GitHub Codespace and Install Terraform & AWS CLI**  

Follow these steps to set up **GitHub Codespaces**, install **Terraform**, and **AWS CLI** to work with AWS resources.  

---

### **Step 1: Enable GitHub Codespaces**  
1. Log in to **GitHub** at [github.com](https://github.com/).  
2. Go to your **repository** where you want to create the Codespace.  
3. Click on **Code** → **Codespaces** → **Create Codespace on main**.  

  ![image](https://github.com/user-attachments/assets/2297f2b2-edad-4a52-a9de-cb785bd9d2d5)
  ![image](https://github.com/user-attachments/assets/da0ee71e-2cd2-4647-bc47-0e43bd2e5d04)

4. Wait for the environment to launch (it may take a few seconds).  

---

### **Step 2: Install Terraform in GitHub Codespaces**  
Once inside the Codespace terminal, run the following commands to install Terraform:  

```sh
# Download Terraform binary
sudo apt update && sudo apt install -y wget unzip

# Get the latest Terraform version
wget https://releases.hashicorp.com/terraform/1.7.5/terraform_1.7.5_linux_amd64.zip

# Unzip and move Terraform binary to /usr/local/bin
unzip terraform_1.7.5_linux_amd64.zip
sudo mv terraform /usr/local/bin/

# Verify the installation
terraform -version
```

If Terraform is installed correctly, you will see the Terraform version displayed.  

![image](https://github.com/user-attachments/assets/744a2871-0ee2-4059-baa3-484c4b6b5767)

---

### **Step 3: Install AWS CLI in GitHub Codespaces**  
To install AWS CLI, run the following commands in the Codespace terminal:  

```sh
# Download AWS CLI installer
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip the installer
unzip awscliv2.zip

# Install AWS CLI
sudo ./aws/install

# Verify the installation
aws --version
```
You should see the **AWS CLI version** displayed, which confirms the installation.  

![image](https://github.com/user-attachments/assets/131723c3-b1c8-4737-bae8-c5270a615465)

---


Now, your GitHub Codespace is ready with **Terraform** and **AWS CLI**. You can start deploying AWS resources using Terraform.

### **Steps to Create an EC2 Instance with Terraform**  

Since you have **GitHub Codespaces** with **Terraform and AWS CLI installed**, follow these steps:  

### **Step 1: Configure AWS CLI**  
To connect AWS CLI with your AWS account, run:  

```sh
aws configure
```
It will ask for:  
- **AWS Access Key ID**  
- **AWS Secret Access Key**  
- **Default region** (e.g., `ap-south-1`)  
- **Output format** (default is `json`)  

Enter the details and press **Enter**.  

---

#### **Step 2: Create a Terraform Configuration File**  
Create a file named **`main.tf`** and add the following code:  

```hcl
provider "aws" {
  region = "ap-south-1" # Change to your AWS region
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-00bb6a80f01f03502" # Replace with the correct AMI ID for your region
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-EC2"
  }
}
```
This file tells Terraform to:  
- Use AWS as the provider  
- Launch an EC2 instance with **t2.micro** type  
- Use a specific **AMI ID**  
- Assign the tag **"Terraform-EC2"** to the instance  

![image](https://github.com/user-attachments/assets/ab018efc-b61c-40ec-b5c1-86a851092daa)

---

#### **Step 3: Initialize Terraform**  
Run the following command to initialize Terraform in your Codespace:  
```sh
terraform init
```
This downloads the necessary Terraform plugins for AWS.  

![image](https://github.com/user-attachments/assets/b42765a8-f4c4-40ba-af97-c504041a840c)

---

#### **Step 4: Validate and Plan the Deployment**  
Run the following command to check for errors:  
```sh
terraform validate
```

![image](https://github.com/user-attachments/assets/7c7d4e2e-b992-4881-b20c-a15da31262ce)

Then, preview what Terraform will create:  
```sh
terraform plan
```

![image](https://github.com/user-attachments/assets/b5e7454d-b0bd-4d20-bc57-c366469a27ae)
![image](https://github.com/user-attachments/assets/c34fa4c3-c40b-4a04-bc12-1c12c462f8cd)

---

#### **Step 5: Apply and Create EC2 Instance**  
Run the following command to deploy your EC2 instance:  
```sh
terraform apply -auto-approve
```
This will create the instance in AWS.  

![image](https://github.com/user-attachments/assets/261c403a-25ec-4371-b639-23cdb82b58be)
![image](https://github.com/user-attachments/assets/a7c69ad1-0e69-4153-925c-fb899658490b)
![image](https://github.com/user-attachments/assets/65e7f021-acda-4f70-b964-adc252c83908)

---

#### **Step 6: Verify the EC2 Instance**  
Check if the instance is created using:  
```sh
aws ec2 describe-instances --query "Reservations[*].Instances[*].InstanceId"
```

![image](https://github.com/user-attachments/assets/e9a24965-6e65-4dab-8ff6-ce0df31f3f23)

Or log in to the **AWS Console** → **EC2 Dashboard** and verify the instance.  

![image](https://github.com/user-attachments/assets/5d1dd96f-6e1e-40b9-a6a9-a1af799c7349)

---

#### **Step 7: Destroy the EC2 Instance (Optional)**  
If you want to delete the EC2 instance, run:  
```sh
terraform destroy -auto-approve
```

![image](https://github.com/user-attachments/assets/00ff38b1-6889-496b-8d89-b50d1f43336f)
![image](https://github.com/user-attachments/assets/9fd68ba3-e31b-4348-a8d6-9a633f4c10e9)

Or go to the **AWS Console** → **EC2 Dashboard** and verify the instance whether it's terminated or not.  

![image](https://github.com/user-attachments/assets/2ab09ea6-d0a8-41d7-b8b4-a4559fdb96a1)

Now your EC2 instance is created and managed using Terraform. 

---
