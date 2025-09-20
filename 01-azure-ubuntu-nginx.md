# 🎬 Netflix Clone Deployment on Azure Ubuntu VM

[![Azure](https://img.shields.io/badge/Azure-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white)](https://azure.microsoft.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)](https://nginx.org/)
[![Netflix](https://img.shields.io/badge/Netflix-E50914?style=for-the-badge&logo=netflix&logoColor=white)](https://netflix.com/)

> **Module 1: Part 1** - Complete guide to deploy a Netflix clone website on Azure using Ubuntu VM and Nginx

## 📋 Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Create Ubuntu VM](#step-1-create-ubuntu-vm-free-tier-eligible)
- [Step 2: Get VM Public IP](#step-2-get-vm-public-ip-address)
- [Step 3: Prepare SSH Key](#step-3-prepare-ssh-key-file)
- [Step 4: Connect via SSH](#step-4-connect-to-vm-via-ssh)
- [Step 5: Install Nginx](#step-5-install-and-configure-nginx)
- [Step 6: Clone Repository](#step-6-clone-netflix-static-website)
- [Step 7: Deploy Website](#step-7-deploy-website-with-nginx)
- [Step 8: Verify Deployment](#step-8-verify-deployment)
- [Troubleshooting](#troubleshooting)
- [Next Steps](#next-steps)

## Prerequisites

- Azure account (free tier eligible or Student Account)
- Basic command line knowledge
- Terminal/PowerShell access

---

## Step 1: Create Ubuntu VM (Free Tier Eligible)

### 1.1 Navigate to Azure Portal
- Go to **Azure Portal** → **Create Resource** → **Virtual Machine**

### 1.2 Configure VM Settings
| Setting | Value |
|---------|-------|
| **Image** | Ubuntu Server 20.04 LTS |
| **Size** | `B1s` (free service eligible) |
| **Authentication** | SSH key |
| **Key pair** | Generate new key pair |

### 1.3 Download SSH Key
After clicking **Create**, a blue button appears → **Download private key (.pem file)**

> ⚠️ **CRITICAL**: If you skip downloading the .pem file, you cannot log in to the VM later!

### 1.4 Configure Networking
Allow these inbound ports:
- `22` (SSH)
- `80` (HTTP) 
- `443` (HTTPS)

---

## Step 2: Get VM Public IP Address

1. Navigate to **Azure Portal** → **Virtual Machines** → **Your VM name**
2. On the **Overview** page, locate the **Public IP Address**
3. Save this IP - you'll need it for SSH and browser access

> 💡 **Note**: Sometimes displayed as "NIC Public IP" - both refer to the same address

---

## Step 3: Prepare SSH Key File

1. Move the downloaded `.pem` file into an **empty folder**
2. Right-click inside that folder → **Open in Terminal/PowerShell**

> ⚠️ **Important**: Do NOT use Command Prompt - use Terminal or PowerShell

---

## Step 4: Connect to VM via SSH

### 4.1 Verify PEM File
```bash
ls
```
You should see your `key.pem` file listed

### 4.2 Connect to VM
```bash
ssh -i key.pem azureuser@<VM_PUBLIC_IP>
```
- Replace `key.pem` with your actual file name
- Replace `<VM_PUBLIC_IP>` with your VM's public IP

### 4.3 Fix Permissions (if needed)
If you get a permissions error:
1. Copy the complete error message
2. Paste it into ChatGPT for the exact fix command
3. Run the suggested `chmod` (Linux/Mac) or `icacls` (Windows) command
4. Retry the SSH connection

---

## Step 5: Install and Configure Nginx

Run these commands on your VM:

```bash
# Update system packages
sudo apt update -y && sudo apt upgrade -y

# Install Nginx web server
sudo apt install nginx -y

# Enable Nginx to start on boot
sudo systemctl enable nginx

# Start Nginx service
sudo systemctl start nginx
```

### ✅ Test Nginx Installation
Open browser and go to: `http://<VM_PUBLIC_IP>`

You should see the default Nginx welcome page.

---

## Step 6: Clone Netflix Static Website

```bash
git clone https://github.com/anshuman018/Netflix-Static.git
```

---

## Step 7: Deploy Website with Nginx

```bash
# Remove default Nginx files
sudo rm -rf /var/www/html/*

# Copy Netflix clone files to web directory
sudo cp -r Netflix-Static/* /var/www/html/

# Restart Nginx to apply changes
sudo systemctl restart nginx
```

---

## Step 8: Verify Deployment

### 🎉 Final Verification
1. Open your web browser
2. Navigate to: `http://<VM_PUBLIC_IP>`
3. You should see the **Netflix static website** running!

---

## 🔧 Troubleshooting

<details>
<summary><b>SSH Permission Errors</b></summary>

**Linux/Mac:**
```bash
chmod 400 key.pem
```

**Windows PowerShell:**
```powershell
icacls key.pem /inheritance:r
icacls key.pem /grant:r "%username%:R"
```
</details>

<details>
<summary><b>Cannot Access Website</b></summary>

1. Check VM is running in Azure Portal
2. Verify ports 80, 443 are open in Network Security Group
3. Ensure Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```
</details>

<details>
<summary><b>Git Clone Issues</b></summary>

If git is not installed:
```bash
sudo apt install git -y
```
</details>

---

## 🚀 Next Steps

Ready for **Module 1: Part 2**? 

### Coming Next:
- [ ] 🔒 **Enable HTTPS with Let's Encrypt (certbot)**
- [ ] 🌐 **Custom domain configuration**  
- [ ] 📜 **SSL certificate setup**
- [ ] ⚡ **Performance optimization**

---

## 📸 Screenshots

### Expected Result:
When you visit `http://<VM_PUBLIC_IP>`, you should see:

```
🎬 Netflix Clone Website
├── Hero Section with Netflix Branding
├── Movie Categories
├── Trending Content
└── Responsive Design
```

---

## 🤝 Contributing

Found an issue or want to improve this guide?

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add some improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## ⭐ Support

If this guide helped you, please give it a ⭐!

**Questions?** Open an [issue](../../issues) and I'll help you out!

---

<div align="center">

**Made with ❤️ for the DevOps Community**

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com)
[![Azure](https://img.shields.io/badge/Microsoft_Azure-0089D0?style=for-the-badge&logo=microsoft-azure&logoColor=white)](https://azure.microsoft.com)

</div>
