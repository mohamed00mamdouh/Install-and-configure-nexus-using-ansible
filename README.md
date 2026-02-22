# Install Nexus using Ansible

This Ansible playbook installs and configures **Nexus** on Ubuntu/Debian-based servers.

The playbook automates the full installation process including dependencies, download, extraction, user creation, permission setup, startup, and verification.

---

## 📌 Overview

This playbook performs the following steps:

1. Installs required dependencies (Java & net-tools)
2. Downloads Nexus Repository Manager 3
3. Extracts and renames the Nexus directory
4. Creates a dedicated `nexus` user and group
5. Sets correct ownership permissions
6. Starts Nexus as the `nexus` user
7. Verifies that Nexus is running

---

## 🧰 Requirements

Before running this playbook, ensure you have:

- Ansible 2.9 or later installed
- Ubuntu/Debian-based target servers
- SSH access to target servers
- Sudo privileges on target machines

---

## 📂 Project Structure

```
.
├── inventory.ini
├── playbook.yml
├── ansible.cfg
└── README.md
```

---

## 🖥️ Inventory Configuration

Create an inventory file named `inventory.ini`:

```ini
[nexues_servers]
server1 ansible_host=YOUR_SERVER_IP ansible_user=ubuntu
```

> ⚠️ Important: The inventory group name **must match** `nexues_servers` as defined in the playbook.

---

## 🚀 Installation Steps Explained

### 1️⃣ Install Dependencies

The playbook installs:

- `openjdk-8-jre-headless`
- `net-tools`

Java is required to run Nexus, and net-tools is used for network verification.

---

### 2️⃣ Download and Extract Nexus

- Downloads Nexus version `3.89.1-02`
- Saves it under `/opt/`
- Extracts the archive
- Renames extracted folder to:

```
/opt/nexus
```

---

### 3️⃣ Create Nexus User and Set Permissions

The playbook:

- Creates a `nexus` group
- Creates a `nexus` user
- Assigns ownership of:
  - `/opt/nexus`
  - `/opt/sonatype-work`

This ensures Nexus does not run as root (security best practice).

---

### 4️⃣ Configure Nexus to Run as nexus User

The playbook sets:

```
run_as_user="nexus"
```

Inside:

```
/opt/nexus/bin/nexus.rc
```

Then starts Nexus using:

```
/opt/nexus/bin/nexus start
```

---

### 5️⃣ Verify Nexus is Running

The playbook:

- Checks running processes using `ps`
- Waits 1 minute for Nexus startup
- Verifies open listening ports using `netstat`

---

## ▶️ How to Run the Playbook

Basic execution:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

If sudo password is required:

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
```

---

## 🌐 Access Nexus

After successful deployment, open your browser:

```
http://YOUR_SERVER_IP:8081
```

---

## 🔐 Retrieve Default Admin Password

After first startup, Nexus generates a temporary admin password.

Retrieve it using:

```bash
cat /opt/sonatype-work/nexus3/admin.password
```

Login with:

- Username: `admin`
- Password: (output from the command above)

---

## 📦 Installed Version

```
nexus-3.89.1-02
```

---

## 📁 Important Directories

| Path | Description |
|------|-------------|
| `/opt/nexus` | Nexus application files |
| `/opt/sonatype-work` | Nexus data directory |

---

## 🔥 Firewall Requirements

Ensure port **8081** is open:

If using UFW:

```bash
sudo ufw allow 8081
```

If using AWS Security Groups:
- Allow inbound TCP port 8081

---

## ⚠️ Production Recommendations

For production environments, consider:

- Using Java 11 or higher
- Creating a systemd service for Nexus
- Configuring a reverse proxy (Nginx/Apache)
- Enabling HTTPS
- Configuring backup for `/opt/sonatype-work`
- Increasing JVM memory settings

---

## 🛠️ Troubleshooting

### Check Nexus Status

```bash
ps aux | grep nexus
```

### Check Listening Ports

```bash
netstat -lntp
```

### View Nexus Logs

```bash
tail -f /opt/sonatype-work/nexus3/log/nexus.log
```

---
### Nexus is up and running
![nexus-image](nexus.png)