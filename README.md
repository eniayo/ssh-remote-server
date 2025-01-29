##SSH Remote Server Setup and Security Configuration

Overview

This project demonstrates how to set up a basic remote Linux server and configure it to allow SSH connections. Additionally, it covers the installation and configuration of fail2ban for Linux and sshguard for macOS to enhance security and prevent brute force attacks.

Table of Contents

System Setup

SSH Key Generation

Adding SSH Keys to the Server

Verifying SSH Access

Configuring SSH Alias

Installing and Configuring fail2ban

Installing and Configuring sshguard on macOS

Conclusion

System Setup

Create a Droplet (Virtual Machine) on DigitalOcean:

Go to the DigitalOcean dashboard.

Select "Deploy a virtual machine."

Choose the size and region of your Droplet.

Select the operating system (e.g., Ubuntu).

Connect to Your Droplet:

ssh root@your-droplet-ip

SSH Key Generation

Generate Two SSH Key Pairs:

ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_first_key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_second_key

Copy the Public Keys:

cat ~/.ssh/id_rsa_first_key.pub
cat ~/.ssh/id_rsa_second_key.pub

Adding SSH Keys to the Server

Create the .ssh Directory and authorized_keys File on the Droplet:

mkdir -p ~/.ssh
touch ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

Add the Public Keys to the authorized_keys File:

echo "your-public-key-from-id_rsa_first_key.pub" >> ~/.ssh/authorized_keys
echo "your-public-key-from-id_rsa_second_key.pub" >> ~/.ssh/authorized_keys

Verifying SSH Access

Disconnect from the Droplet:

exit

Connect Using Both SSH Keys:

ssh -i ~/.ssh/id_rsa_first_key root@your-droplet-ip
ssh -i ~/.ssh/id_rsa_second_key root@your-droplet-ip

Configuring SSH Alias

Edit the SSH Configuration File on Your Local Machine:

nano ~/.ssh/config

Add Aliases:

Host droplet1
    HostName your-droplet-ip
    User root
    IdentityFile ~/.ssh/id_rsa_first_key

Host droplet2
    HostName your-droplet-ip
    User root
    IdentityFile ~/.ssh/id_rsa_second_key

Save and Close the File: Press Ctrl+O, Enter, Ctrl+X.

Connect Using Aliases:

ssh droplet1
ssh droplet2

Installing and Configuring fail2ban

For Linux Servers:

Install fail2ban:

sudo apt-get update
sudo apt-get install fail2ban

Configure fail2ban:

sudo nano /etc/fail2ban/jail.local

Add the SSH Configuration:

[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 5

Save and Exit: Press Ctrl+O, Enter, Ctrl+X.

Restart and Check fail2ban:

sudo systemctl restart fail2ban
sudo systemctl status fail2ban

Installing and Configuring sshguard on macOS

For macOS Systems:

Install sshguard Using Homebrew:

brew install sshguard

Create the plist File for sshguard:

sudo nano /Library/LaunchDaemons/com.homebrew.sshguard.plist

Add the plist Configuration:

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

Save and Set Permissions:

sudo chmod 644 /Library/LaunchDaemons/com.homebrew.sshguard.plist
sudo chown root:wheel /Library/LaunchDaemons/com.homebrew.sshguard.plist

Load and Start sshguard:

sudo launchctl bootstrap system /Library/LaunchDaemons/com.homebrew.sshguard.plist
sudo launchctl start com.homebrew.sshguard
sudo launchctl list | grep sshguard

Conclusion

By following these steps, you can successfully set up a remote Linux server and configure it to allow SSH connections using multiple key pairs. Additionally, you have implemented security measures to prevent brute force attacks on both Linux and macOS systems.
