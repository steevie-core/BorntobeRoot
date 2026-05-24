*This project has been created as part of the 42 curriculum by limelo-c.*

# Born2beRoot

---

## Description

This project is a project at 42 teaches us how to create our first machine in VirtualBox. 

The 

**Goal:** Being able to set up our own operating system while implementing strict rules.
**Overview:** Using an operating system such as `Debian` in my specific project, the set up was done without using any graphical interface. Multiple encrypted partitions using LVM were created, sudo was configured, creating a user, a group and adding a user to a group. SSH configuration, changing ports and disabling root login via SSH. Installing and configuring UFW package, enabling a firewall. 

---

## Instructions

This project consists of the VirtualMachine setup and a `signature.txt`, consisting of the signature of the machine.
In order to test this project you will have to follow these steps. 

1. Clone the repository:

```bash
$ git clone <repository-url>
$ cd born2beroot
```

2. Launch your virtual machine using VirtualBox or UTM.

3. Follow the steps in this README to configure:

- Sudo privileges

- SSH server and firewall

- User accounts and groups

- Password policies

- Cron jobs

4. Verify that all services are running correctly:

```bash
$ sudo systemctl status ssh
$ sudo ufw status
$ getent passwd
$ sudo crontab -u root -l
$ sudo crontab -e
```

---
## Comparisons

| Feature          | Choice in Project | Alternative | Notes                                                                                           |
| ---------------- | ----------------- | ----------- | ----------------------------------------------------------------------------------------------- |
| Operating System | Debian            | Rocky Linux | Debian is stable with extensive documentation; Rocky offers enterprise-level SELinux by default |
| Security Module  | AppArmor          | SELinux     | AppArmor is simpler to configure; SELinux is more for high-security environments and complex    |
| Firewall         | UFW               | firewalld   | UFW is user-friendly; firewalld provides more advanced rules for enterprises                    |
| Virtualization   | VirtualBox        | UTM         | VirtualBox is widely used and well-documented; UTM is optimized for macOS M1/M2                 |


# Execution

## Installation

- Set up the VM in **VirtualBox** (or UTM) without a graphical interface.  
- Create the hostname with your login followed by `42` (e.g., `limelo-c42`).  
- Configure encrypted partitions using **LVM** according to subject requirements.  

--- 

## sudo

### Step 1: Installing sudo
```bash
$ su -
# apt install sudo
# dpkg -l | grep sudo
```

### Step 2: Adding User to sudo Group

```bash
# adduser <username> sudo
# OR
# usermod -aG sudo <username>
$ getent group sudo
# reboot
$ sudo -v
```

### Step 3: Running root-Privileged Commands

Run all root commands using sudo, for example:

```bash
$ sudo apt update
```

### Step 4: Configuring sudo

Edit or create /etc/sudoers.d/<filename> and add:

```bash
Defaults        passwd_tries=3
Defaults        badpass_message="<custom-error-message>"
Defaults        logfile="/var/log/sudo/<filename>"
Defaults        log_input,log_output
Defaults        iolog_dir="/var/log/sudo"
Defaults        requiretty
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

Create the log directory:

```bash
$ sudo mkdir /var/log/sudo
```

## SSH

### Step 1: Installing & Configuring SSH

```bash
$ sudo apt install openssh-server
$ dpkg -l | grep ssh
$ sudo vi /etc/ssh/sshd_config
```

Configuration:

```text
Port 4242
PermitRootLogin no
```

Restart and check:

```bash
$ sudo service ssh status
$ systemctl status ssh
```

### Step 2: Installing & Configuring UFW

```bash
$ sudo apt install ufw
$ dpkg -l | grep ufw
$ sudo ufw allow 4242
$ sudo ufw enable
$ sudo ufw status
```

### Step 3: Connecting to Server via SSH

```bash
$ ssh <username>@<ip-address> -p 4242
$ logout
$ exit
```


## User Management
### Step 1: Setting Up a Strong Password Policy

Edit /etc/login.defs:

```bash
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
PASS_WARN_AGE   7
```

Install PAM password quality module:

```bash
$ sudo apt install libpam-pwquality
$ dpkg -l | grep libpam-pwquality
$ sudo vi /etc/pam.d/common-password
```

Final line example:

```bash
password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

### Step 2: Creating a New User

```bash
$ sudo adduser <username>
$ getent passwd <username>
$ sudo change -l <username>
```

### Step 3: Creating a New Group

```bash
$ sudo addgroup user42
$ sudo adduser <username> user42
$ sudo usermod -aG user42 <username>
$ getent group user42
```

## cron

Edit cron as root:

```bash
$ sudo crontab -u root -e
```

To run a script every 10 minutes:

```bash
*/10 * * * * sh /path/to/script
```

Check scheduled jobs:

```bash
$ sudo crontab -u root -l
```
---

## Resources

- [Debian Documentation](https://www.debian.org/doc/)
- [Cron Manual](https://man7.org/linux/man-pages/man5/crontab.5.html)
- [UFW Documentation](https://help.ubuntu.com/community/UFW)
- [PAM Password Quality](https://linux.die.net/man/8/pam_pwquality)
- Tutorials on VirtualBox, UTM, LVM, and SSH configuration
	
AI usage:
	- No usage.
