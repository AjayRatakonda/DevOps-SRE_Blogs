# **How to install terraform and aws cli on windows and how to Create an AWS EC2 Instance Using Terraform**

This guide explains how to create an EC2 instance using Terraform with three configuration files: `main.tf`, `variables.tf`, and `terraform.tfvars`.

---
## **1.Install Terraform and AWS CLI on windows**
- download terraform in windows using below link
  
  https://developer.hashicorp.com/terraform/install

  ![image](https://github.com/user-attachments/assets/d88574ea-b86c-47f3-9c0f-58ad430d6ce1)

-  and extract zip file and move terraform.exe to c:\terraform like below screenshot

   ![image](https://github.com/user-attachments/assets/bac59f03-ff85-49d5-82ba-da35ad0528d0)

- add terraform to system path

  search edit the system Environment Variables.

  ![image](https://github.com/user-attachments/assets/0a1ea5a9-2f8f-4cd4-ae53-5434da7bfdbc)

  Under System Variables, find Path and click Edit.
  Click New, then add C:\terraform.
  Click OK and close the windows.

- verify installation, open command prompt(cmd), powershell.

  ```bash
  terraform -version
  ```
  ![image](https://github.com/user-attachments/assets/32143cbe-64c7-4ac5-a57b-d0503b817273)

- install aws cli on windows like below

  https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

  ![image](https://github.com/user-attachments/assets/ceb5acc6-f43b-44c6-8aab-c53d94157195)

  and click on awscli.msi file and install and check aws cli is installed or not

  ```bash
  aws --version
  ```
  ![image](https://github.com/user-attachments/assets/5000070c-12d4-4839-bac4-73be6d41b537)

## **2. Create Terraform Configuration Files**

### **Create `main.tf`** (Defines EC2 instance)
```hcl
provider "aws" {
  region = var.aws_region
}

resource "aws_instance" "my_instance" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = var.instance_name
  }
}
```
![image](https://github.com/user-attachments/assets/02e4bfdd-6877-42da-bd25-4d95016a4929)


### **Create `variables.tf`** (Defines input variables)
```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
}

variable "ami_id" {
  description = "Amazon Machine Image ID"
  type        = string
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
}

variable "instance_name" {
  description = "Name tag for EC2 instance"
  type        = string
}
```
![image](https://github.com/user-attachments/assets/9e212e17-bd47-476a-a244-cd3b4a102c71)

### **Create `terraform.tfvars`** (Assigns values to variables)
```hcl
aws_region     = "ap-south-1"
ami_id         = "ami-00bb6a80f01f03502"  # Change based on region
instance_type  = "t2.micro"
instance_name  = "Terraform-EC2"
```
![image](https://github.com/user-attachments/assets/d548fb55-af90-44fb-a782-016502403601)

---

## **3. Initialize and Apply Terraform**

### **Initialize Terraform**
```bash
terraform init
```
![image](https://github.com/user-attachments/assets/cf1230ec-487c-43e9-9bee-27e086c4dacf)

### **Validate Configuration**
```bash
terraform validate
```
![image](https://github.com/user-attachments/assets/b296067d-424a-401f-975d-d4817eceb6f3)

### **View Execution Plan**
```bash
terraform plan
```
![image](https://github.com/user-attachments/assets/266c0201-aa74-499e-a7b4-f2dab6193fc5)
![image](https://github.com/user-attachments/assets/dcaf52f8-f928-4558-96e5-36ebb8daa45c)
![image](https://github.com/user-attachments/assets/e81560a4-c6d2-47c8-84d3-a7b53138a7db)

### **Apply Configuration (Create EC2 Instance)**
```bash
terraform apply -auto-approve
```
![image](https://github.com/user-attachments/assets/2783c236-0f66-4c1d-96ae-835587e44386)
![image](https://github.com/user-attachments/assets/4f972584-9459-42b6-9292-f0a0c612468b)

Once completed, the EC2 instance will be created in AWS.

![image](https://github.com/user-attachments/assets/8c08d06e-74d0-49db-a3ac-c6a3a24beeba)

---

## **4. Verify the Instance**

### **Check in AWS Console**
1. Open **AWS Console** → **EC2 Dashboard**
2. Find the instance with the **tag** `Terraform-EC2`

![image](https://github.com/user-attachments/assets/adf0efd5-5ea3-4087-bee0-b4b8aaba14e5)

### **Check Using AWS CLI**
```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=Terraform-EC2" --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table
```
![image](https://github.com/user-attachments/assets/ccde2e2b-4da4-487f-b6c1-8ed9a38928ef)

### **5. Output Variables**
Used to display resource details after applying Terraform.
define outputs in output.tf

```hcl
output "public_ip" {
  description = "Public IP of the instance"
  value       = aws_instance.my_instance.public_ip
}
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.my_instance.id
}
```
![image](https://github.com/user-attachments/assets/842acd79-98ab-4387-aefb-f61862612172)

apply terraform and view outputs

```bash
terraform init
terraform apply
```

![image](https://github.com/user-attachments/assets/dd4a5fb2-c99c-4e5e-b850-c406aaf1a366)

---

## **5. Destroy the Instance (Clean Up)**
If you no longer need the instance:
```bash
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/429544c6-a438-4f0a-9247-b80cf5dd680d)
![image](https://github.com/user-attachments/assets/9904b5a2-8a7f-44e6-ad8a-cdc401570f8d)
![image](https://github.com/user-attachments/assets/dc032c47-0cec-46ed-a0e0-60613c303692)
![image](https://github.com/user-attachments/assets/34acc546-98ac-4917-8263-421e088fc042)

---
