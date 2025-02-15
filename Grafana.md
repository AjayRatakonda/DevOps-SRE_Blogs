# **How to install Grafana in AWS Ubuntu spot instace**
Here’s a step-by-step guide to installing **Grafana** on an **AWS Ubuntu 22.04 Spot Instance** with all the prerequisites.  

---

### **Step 1: Launch an AWS Spot Instance (Ubuntu 22.04)**
1. Log in to **AWS Console**.
2. Navigate to **EC2** > **Instances** > **Launch Instances**.
3. Select **Ubuntu 22.04** as the OS.
4. Choose an **Instance Type** (t2.micro is enough for testing).
5. Under **Purchasing option**, select **Request Spot Instance**.
6. Configure **Security Group**:
   - Allow **Inbound Rules** for:
     - **SSH (22)**
     - **Grafana UI (3000)**
7. **Launch the instance** and connect via SSH.

---

### **Step 2: Connect to the Instance**
Use SSH to connect to the instance:
```bash
ssh -i your-key.pem ubuntu@your-instance-ip
```

---

### **Step 3: Update the System**
```bash
sudo apt update && sudo apt upgrade -y
```

---

### **Step 4: Install Dependencies**
Ensure required packages are installed:
```bash
sudo apt install -y software-properties-common apt-transport-https wget
```

---

### **Step 5: Add the Grafana Repository**
```bash
sudo mkdir -p /etc/apt/keyrings
wget -q -O - https://packages.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.asc
echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

---

### **Step 6: Install Grafana**
```bash
sudo apt update
sudo apt install -y grafana
```

---

### **Step 7: Start & Enable Grafana Service**
```bash
sudo systemctl enable --now grafana-server
```

Check if Grafana is running:
```bash
sudo systemctl status grafana-server
```

---

### **Step 8: Open Port 3000 in AWS Security Group**
1. Go to **EC2 Dashboard** > **Security Groups**.
2. Find your instance's **Security Group**.
3. Add an **Inbound Rule**:
   - **Type:** Custom TCP
   - **Port:** 3000
   - **Source:** 0.0.0.0/0 (or restrict it to your IP)

---

### **Step 9: Access Grafana Web Interface**
Open your browser and go to:
```
http://your-instance-ip:3000
```

---

### **Step 10: Login to Grafana**
- **Default Username:** `admin`
- **Default Password:** `admin`
- Change the password on first login.

---

### **Verification**
Check if Grafana is running:
```bash
sudo systemctl status grafana-server
```

---

### **Grafana Installation Completed! 🎉**
