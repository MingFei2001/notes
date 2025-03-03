# Secure Samba (SMB) Server on Linode

This guide walks you through setting up a **secure, professional-grade Samba (SMB) server** on a **Linode VPS**. We ensure **proper authentication, user permissions, and security best practices** to keep your shared files safe.

## 🚀 Features
- **User authentication**: Only authorized users can access the share.
- **Secure permissions**: Restricts access to designated users.
- **Firewall protection**: Limits SMB access to necessary ports.
- **Brute-force protection**: Uses fail2ban to block repeated login attempts.

---

## 📌 Prerequisites
- A fresh Linode server (Debian/Ubuntu).
- A non-root user with sudo access.

---

## 🛠️ Installation & Setup

### 1️⃣ Add a User with Sudo Privileges
```bash
sudo usermod -aG sudo mingfei
su - mingfei
```

### 2️⃣ Install Samba & SMB Client
```bash
sudo apt update && sudo apt install samba smbclient -y
```

### 3️⃣ Create an SMB Group & Add Users
```bash
sudo groupadd smbgroup
sudo usermod -aG smbgroup mingfei
sudo smbpasswd -a mingfei
```

### 4️⃣ Configure Samba & Define the Share
```bash
sudo nano /etc/samba/smb.conf
```
Add the following at the end:
```ini
[themfshare]
   path = /srv/smb/themfshare
   valid users = @smbgroup
   guest ok = no
   writable = yes
   browsable = yes
   create mask = 0770
   directory mask = 0770
```
Save and exit.

### 5️⃣ Create & Secure the Shared Directory
```bash
sudo mkdir -p /srv/smb/themfshare
sudo chown -R root:smbgroup /srv/smb/themfshare
sudo chmod -R 770 /srv/smb/themfshare
```

### 6️⃣ Restart & Enable Samba
```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
sudo systemctl status smbd
```

### 7️⃣ Configure Firewall for SMB
```bash
sudo ufw allow 139,445/tcp
sudo ufw allow 137,138/udp
sudo ufw reload
```

### 8️⃣ Test the Configuration
```bash
testparm
smbclient -L //localhost -U mingfei
smbclient //localhost/themfshare -U mingfei
ls
```

---

## 🔒 Security Enhancements

### 🚫 Disable Guest Access
Edit `/etc/samba/smb.conf` under `[global]`:
```ini
map to guest = Never
```
Restart Samba:
```bash
sudo systemctl restart smbd
```

### 🛡️ Protect Against Brute-Force Attacks with Fail2Ban
```bash
sudo apt install fail2ban -y
sudo nano /etc/fail2ban/jail.local
```
Add:
```ini
[smb]
enabled = true
port = 445
filter = smb
logpath = /var/log/samba/log.smbd
maxretry = 5
bantime = 3600
```
Restart fail2ban:
```bash
sudo systemctl restart fail2ban
```

---

## ✅ Verification & Management
### 🔍 List SMB Users
```bash
pdbedit -L
```
### 🧑‍💻 Verify Group Members
```bash
getent group smbgroup
```
### ❌ Remove Unwanted Users
```bash
sudo deluser <username> smbgroup
```

## ➕ Adding a New SMB User  
To add a new user (e.g., **user**) to the SMB server:  

### 1️⃣ Create the User (If Not Already Exists)  
If the user does not already exist on the system, create it:  
```bash
sudo adduser user
```
Follow the prompts to set a system password (this is separate from the SMB password).

### 2️⃣ Add the User to the SMB Group
```bash
sudo usermod -aG smbgroup user
```

### 3️⃣ Set an SMB Password
```bash
sudo smbpasswd -a user
```
You will be prompted to set an SMB password—this is the password user will use to access the shared folder.

### 4️⃣ Verify the User is in the SMB Group
```bash
getent group smbgroup
```
Ensure user appears in the output alongside other authorized users.

### 5️⃣ Test SMB Access for the New Useer
List available shares:
```bash
smbclient -L //localhost -U user
```
Try accessing themfshare:
```bash
smbclient //localhost/themfshare -U user
ls
```

---

## 🎉 Conclusion
You now have a **fully functional, secure Samba (SMB) server** on Linode! 

🔹 **Secure authentication** ensures only authorized users can access your share.
🔹 **Fail2Ban** and **firewall rules** enhance security.
🔹 Works seamlessly across **Windows, Linux, and macOS**.

🚀 **Have questions? Open an issue or contribute to this guide!**
