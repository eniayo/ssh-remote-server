# SSH Remote Server Setup and Security Configuration

## Overview
This project demonstrates how to set up a basic remote Linux server and configure it to allow SSH connections. Additionally, it covers the installation and configuration of `fail2ban` for Linux and `sshguard` for macOS to enhance security and prevent brute force attacks.

---

## 📌 Table of Contents

1. [System Setup](#system-setup)
2. [SSH Key Generation](#ssh-key-generation)
3. [Adding SSH Keys to the Server](#adding-ssh-keys-to-the-server)
4. [Verifying SSH Access](#verifying-ssh-access)
5. [Configuring SSH Alias](#configuring-ssh-alias)
6. [Installing and Configuring fail2ban](#installing-and-configuring-fail2ban)
7. [Installing and Configuring sshguard on macOS](#installing-and-configuring-sshguard-on-macos)
8. [Conclusion](#conclusion)

---

## ⚙️ System Setup

### Create a Droplet (Virtual Machine) on DigitalOcean:
1. Go to the [DigitalOcean dashboard](https://www.digitalocean.com/).
2. Select **"Deploy a virtual machine"**.
3. Choose the size and region of your Droplet.
4. Select the operating system (e.g., **Ubuntu**).

### Connect to Your Droplet:
```bash
ssh root@your-droplet-ip
```

---

## 🔑 SSH Key Generation

### Generate Two SSH Key Pairs:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_first_key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_second_key
```

### Copy the Public Keys:
```bash
cat ~/.ssh/id_rsa_first_key.pub
cat ~/.ssh/id_rsa_second_key.pub
```

---

## 🔓 Adding SSH Keys to the Server

### Create the `.ssh` Directory and `authorized_keys` File on the Droplet:
```bash
mkdir -p ~/.ssh
touch ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Add the Public Keys to the `authorized_keys` File:
```bash
echo "your-public-key-from-id_rsa_first_key.pub" >> ~/.ssh/authorized_keys
echo "your-public-key-from-id_rsa_second_key.pub" >> ~/.ssh/authorized_keys
```

---

## ✅ Verifying SSH Access

### Disconnect from the Droplet:
```bash
exit
```

### Connect Using Both SSH Keys:
```bash
ssh -i ~/.ssh/id_rsa_first_key root@your-droplet-ip
ssh -i ~/.ssh/id_rsa_second_key root@your-droplet-ip
```

---

## 🔄 Configuring SSH Alias

### Edit the SSH Configuration File on Your Local Machine:
```bash
nano ~/.ssh/config
```

### Add Aliases:
```ini
Host droplet1
    HostName your-droplet-ip
    User root
    IdentityFile ~/.ssh/id_rsa_first_key

Host droplet2
    HostName your-droplet-ip
    User root
    IdentityFile ~/.ssh/id_rsa_second_key
```

### Save and Close the File:
Press `CTRL+O`, then `Enter`, and finally `CTRL+X`.

### Connect Using Aliases:
```bash
ssh droplet1
ssh droplet2
```

---

## 🛡 Installing and Configuring `fail2ban`

### For Linux Servers:

#### Install `fail2ban`:
```bash
sudo apt-get update
sudo apt-get install fail2ban
```

#### Configure `fail2ban`:
```bash
sudo nano /etc/fail2ban/jail.local
```

#### Add the SSH Configuration:
```ini
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 5
```

#### Save and Exit:
Press `CTRL+O`, then `Enter`, and finally `CTRL+X`.

#### Restart and Check `fail2ban`:
```bash
sudo systemctl restart fail2ban
sudo systemctl status fail2ban
```

---

## 🍏 Installing and Configuring `sshguard` on macOS

### For macOS Systems:

#### Install `sshguard` Using Homebrew:
```bash
brew install sshguard
```

#### Create the plist File for `sshguard`:
```bash
sudo nano /Library/LaunchDaemons/com.homebrew.sshguard.plist
```

#### Add the plist Configuration:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.homebrew.sshguard</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/sbin/sshguard</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardErrorPath</key>
    <string>/usr/local/var/log/sshguard/sshguard-stderr.log</string>
    <key>StandardOutPath</key>
    <string>/usr/local/var/log/sshguard/sshguard-stdout.log</string>
</dict>
</plist>
```

#### Save and Set Permissions:
```bash
sudo chmod 644 /Library/LaunchDaemons/com.homebrew.sshguard.plist
sudo chown root:wheel /Library/LaunchDaemons/com.homebrew.sshguard.plist
```

#### Load and Start `sshguard`:
```bash
sudo launchctl bootstrap system /Library/LaunchDaemons/com.homebrew.sshguard.plist
sudo launchctl start com.homebrew.sshguard
sudo launchctl list | grep sshguard
```
https://roadmap.sh/projects/ssh-remote-server-setup
---

## 🎯 Conclusion
By following these steps, you can successfully set up a remote Linux server and configure it to allow SSH connections using multiple key pairs. Additionally, you have implemented security measures to prevent brute force attacks on both Linux and macOS systems.
