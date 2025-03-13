# **How to Create an AWS EC2 Instance Using Terraform**

This guide explains how to create an EC2 instance using Terraform with three configuration files: `main.tf`, `variables.tf`, and `terraform.tfvars`.

---
## **1.Install Terraform and AWS CLI on windows**
- download terraform in windows using below link
  
  https://developer.hashicorp.com/terraform/install

  ![image](https://github.com/user-attachments/assets/dd559dc2-c844-4e02-88a3-1a927b8e45be)

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

### **Create `terraform.tfvars`** (Assigns values to variables)
```hcl
aws_region     = "ap-south-1"
ami_id         = "ami-0c55b159cbfafe1f0"  # Change based on region
instance_type  = "t2.micro"
instance_name  = "Terraform-EC2"
```

---

## **3. Initialize and Apply Terraform**

### **Initialize Terraform**
```bash
terraform init
```

### **Validate Configuration**
```bash
terraform validate
```

### **View Execution Plan**
```bash
terraform plan
```

### **Apply Configuration (Create EC2 Instance)**
```bash
terraform apply -auto-approve
```

Once completed, the EC2 instance will be created in AWS.

---

## **4. Verify the Instance**

### **Check in AWS Console**
1. Open **AWS Console** → **EC2 Dashboard**
2. Find the instance with the **tag** `Terraform-EC2`

### **Check Using AWS CLI**
```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=Terraform-EC2" --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table
```

---

## **5. Destroy the Instance (Clean Up)**
If you no longer need the instance:
```bash
terraform destroy -auto-approve
```

---

## **Conclusion**
You have successfully created an EC2 instance using Terraform. Modify the configuration to add **security groups, key pairs, and EBS volumes** to enhance your setup.
