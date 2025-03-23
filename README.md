# **Deploying OpenStack on an EC2 Instance**

This document provides a comprehensive guide on setting up **OpenStack** on an **AWS EC2 instance** using DevStack. By following these steps, you will successfully deploy OpenStack and access the Horizon dashboard for cloud management.

---

## **Prerequisites**

### **1. AWS EC2 Instance Requirements**

- **AMI (Operating System):** Ubuntu 22.04 LTS (Latest Stable Version)
- **Instance Type:** `t2.large` (Minimum: 2 vCPUs, 8GB RAM)
- **Storage:** Allocate at least **30GB** (default 8GB is insufficient for OpenStack)
- **Key Pair:** Generate or use an existing SSH key for secure authentication.
- **Security Group Rules:**
  - **SSH (22):** Allow access from your IP.
  - **HTTP (80, 443):** Open access for the OpenStack Horizon dashboard.

(![Screenshot 2025-03-23 195914](https://github.com/user-attachments/assets/13f9ad28-60e7-49eb-a211-87a056237b48)


(![Screenshot 2025-03-23 201119](https://github.com/user-attachments/assets/85e1def9-b4a4-4ac6-bd66-43be910b0700)



### **2. Software Requirements**
- **PuTTY or Terminal** for SSH access
- **PuTTYgen** (if using PuTTY on Windows) for key conversion
- **Git** (installed on the instance)
- **Python 3** and required dependencies

---

## **1. Connecting to the EC2 Instance**

### **For Windows Users (Using PuTTY)**

1. **Convert PEM to PPK (PuTTY Private Key Format)**:
   - Open **PuTTYgen**.
   - Click **Load**, select your `.pem` file.
   - Click **Save private key** and save it as `.ppk`.

2. **Launch PuTTY and Connect to EC2**:
   - Open **PuTTY**.
   - In **Host Name**, enter the public IP of the EC2 instance.
   - Navigate to **Connection > SSH > Auth**.
   - Click **Browse**, select the `.ppk` file.
   - Click **Open** and log in as `ubuntu`.

### **For macOS & Linux Users (Using Terminal)**

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

---

## **2. Update System and Install Dependencies**

Before proceeding with the installation, update and upgrade the system packages:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3-dev python3-pip net-tools
```

---

## **3. Clone the DevStack Repository**

Navigate to your home directory and clone the latest DevStack repository:

```bash
git clone https://opendev.org/openstack/devstack.git
cd devstack
```
![image](https://github.com/user-attachments/assets/cbb053f4-e9d8-4140-a47c-64107f440dde)

---

## **4. Create a DevStack Configuration File**

Create and edit a `local.conf` file to customize the OpenStack installation:

```bash
nano local.conf
```

Paste the following configuration:

```ini
[[local|localrc]]
ADMIN_PASSWORD=SuperSecret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

# Automatically detect the host IP
HOST_IP=$(hostname -I | awk '{print $1}')

# Enable only essential services
disable_service tempest
disable_service cinder
disable_service swift
disable_service heat

# Use the latest OpenStack release
USE_PYTHON3=True
GIT_BASE=https://opendev.org
```

Save and exit (CTRL+X, then Y, then ENTER).

![image](https://github.com/user-attachments/assets/ad725943-6387-4e97-9fd2-b4dcad91803b)

---

## **5. Install OpenStack Using DevStack**

Run the DevStack installation script, which may take **30-45 minutes**:

```bash
./stack.sh
```
![image](https://github.com/user-attachments/assets/0dd8d73a-ee79-4910-bd01-ad4c4680ff62)

If the installation is successful, you will see a confirmation message: `DevStack installed successfully!`.

---

## **6. Access the OpenStack Horizon Dashboard**

Once the installation is complete, access the OpenStack **Horizon UI** by opening a web browser and navigating to:

```bash
http://your-ec2-public-ip/dashboard
```

### **Login Credentials:**
- **Username:** `admin`
- **Password:** `SuperSecret` (as defined in `local.conf`)

![image](https://github.com/user-attachments/assets/92629a6b-4ab1-4833-9bb3-de18b532fd0d)

![image](https://github.com/user-attachments/assets/afedc404-8489-4a3a-8ea2-c02d8787ed6c)

---

## **7. Verify OpenStack Services**

Before proceeding with cloud management, confirm that OpenStack services are running:

```bash
source openrc admin admin
openstack service list
```

If successful, you should see a list of active OpenStack services.

![image](https://github.com/user-attachments/assets/4864989c-a066-4a55-a86e-b23841f5f273)

---

## **8. Conclusion**

You have successfully deployed OpenStack on an AWS EC2 instance. You can now use the Horizon Dashboard or OpenStack CLI to manage cloud instances, networking, and storage.

For more advanced configurations and troubleshooting, refer to the [DevStack Documentation](https://github.com/openstack/devstack).

