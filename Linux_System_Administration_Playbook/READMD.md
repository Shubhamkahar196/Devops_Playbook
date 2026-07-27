# 🚀  Linux System Administration Guide

A beginner-friendly hands-on guide for solving 18 essential Enterprise Linux Tasks. Designed for DevOps Engineers, System Administrators, and Cloud Engineers.

---

## Table of Contents
1. [Custom Apache User Setup](#1-custom-apache-user-setup)
2. [Group Creation and User Assignment](#2-group-creation-and-user-assignment)
3. [Linux User Setup with Non-Interactive Shell](#3-linux-user-setup-with-non-interactive-shell)
4. [Service User Creation Without Home Directory](#4-service-user-creation-without-home-directory)
5. [Temporary User Setup with Expiry Date](#5-temporary-user-setup-with-expiry-date)
6. [Linux User Data Transfer](#6-linux-user-data-transfer)
7. [Secure Root SSH Access](#7-secure-root-ssh-access)
8. [Data Backup for Developer](#8-data-backup-for-developer)
9. [Script Execution Permissions](#9-script-execution-permissions)
10. [File Permission Correction](#10-file-permission-correction)
11. [String Replacement Across Configurations](#11-string-replacement-across-configurations)
12. [Secure Data Transfer Across Hosts](#12-secure-data-transfer-across-hosts)
13. [Restrict Cron Access](#13-restrict-cron-access)
14. [Default GUI / CLI Boot Configuration](#14-default-gui--cli-boot-configuration)
15. [Timezone Alignment](#15-timezone-alignment)
16. [Firewall Port Configuration](#16-firewall-port-configuration)
17. [Process Limit Adjustment](#17-process-limit-adjustment)
18. [SELinux Installation and Configuration](#18-selinux-installation-and-configuration)

---


---

# 1️⃣ Custom Apache User Setup


## 📌 Task Overview

 Configure the Apache HTTP web server to run under a dedicated system user instead of the default user for better process isolation and security.
## 🎯 Why It Matters

 Running web servers under generic or privileged users increases security risks if the application gets compromised.

## 💻 Commands to Run
```bash
sudo useradd -r -s /sbin/nologin apache_user
sudo sed -i 's/^User .*/User apache_user/' /etc/httpd/conf/httpd.conf
sudo systemctl restart httpd

## 📖 Quick Breakdown

useradd -r -s /sbin/nologin apache_user: Creates a system user with no SSH/interactive shell access.

sed -i ...: Updates the User directive in the Apache configuration file to apache_user.

systemctl restart httpd: Restarts Apache to apply the new configuration.

systemctl restart httpd: Restarts Apache to apply the new configuration.

# 2. Group Creation and User Assignment
## 📌 Task Overview

Create a new administrative group (sysadmin_group) and assign a user (mohammed) to it.

## 🎯 Why It Matters

Groups allow managing permissions for multiple users easily instead of setting permissions per user.

## 💻 Commands to Run
Bash
sudo groupadd sysadmin_group
sudo useradd -G sysadmin_group mohammed
id mohammed
## 📖 Quick Breakdown
groupadd sysadmin_group: Creates a new secondary group.

useradd -G sysadmin_group mohammed: Creates user and attaches them to sysadmin_group (Use usermod -aG if user exists).

id mohammed: Displays user ID and group memberships to verify.

3. Linux User Setup with Non-Interactive Shell
## 📌 Task Overview

Create a user account that cannot log in interactively via SSH or terminal shell.

## 🎯 Why It Matters

Prevents service accounts from being exploited as an entry point for unauthorized remote access.

## 💻 Commands to Run
Bash
sudo useradd -s /sbin/nologin restricted_user
grep restricted_user /etc/passwd
## 📖 Quick Breakdown
useradd -s /sbin/nologin restricted_user: Sets default shell to /sbin/nologin to block direct shell access.

grep ...: Verifies the shell path assigned in /etc/passwd.

4. Service User Creation Without Home Directory
## 📌 Task Overview

Create a dedicated background service account without generating a /home directory.

## 🎯 Why It Matters

Background services don't need home folders, saving storage and keeping the filesystem clean.

## 💻 Commands to Run
Bash
sudo useradd -M -r -s /sbin/nologin service_user
ls /home/
## 📖 Quick Breakdown
useradd -M -r -s /sbin/nologin service_user: -M explicitly skips home directory creation.

ls /home/: Confirms that no home directory was generated for service_user.

5. Temporary User Setup with Expiry Date
## 📌 Task Overview

Create a temporary contractor account that automatically expires on a specified date.

## 🎯 Why It Matters

Ensures accounts are disabled automatically after contract completion without manual intervention.

## 💻 Commands to Run
Bash
sudo useradd -e 2026-12-31 temp_user
sudo chage -l temp_user
## 📖 Quick Breakdown
useradd -e YYYY-MM-DD temp_user: Sets an explicit expiration date on the account.

chage -l temp_user: Checks account aging and confirms expiry details.

6. Linux User Data Transfer
## 📌 Task Overview

Move files to a user's directory and assign strict ownership and file permissions.

## 🎯 Why It Matters

Ensures targeted user access while enforcing least-privilege security controls.

## 💻 Commands to Run
Bash
sudo mv /tmp/data /home/target_user/
sudo chown -R target_user:sysadmin_group /home/target_user/data
sudo chmod -R 750 /home/target_user/data
## 📖 Quick Breakdown
mv /tmp/data ...: Relocates the folder to target user path.

chown -R owner:group ...: Changes ownership recursively for user and group.

chmod -R 750 ...: Gives full access to owner, read/execute to group, and zero access to others.

7. Secure Root SSH Access
## 📌 Task Overview

Disable direct root user login over SSH connections.

## 🎯 Why It Matters

Forces admins to log in with named accounts and elevate using sudo, creating an active audit trail.

## 💻 Commands to Run
Bash
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
## 📖 Quick Breakdown
sed -i ...: Replaces PermitRootLogin with no in /etc/ssh/sshd_config.

systemctl restart sshd: Restarts the SSH service to enforce root restriction.

8. Data Backup for Developer
## 📌 Task Overview

Compress developer web directory into a .tar.gz archive and assign proper ownership.

## 🎯 Why It Matters

Ensures code backups can be created easily and safely accessed by authorized developers.

## 💻 Commands to Run
Bash
sudo tar -czvf /home/thor/backup.tar.gz /var/www/html/
sudo chown thor:thor /home/thor/backup.tar.gz
## 📖 Quick Breakdown
tar -czvf ...: Compresses target directory using gzip into a single tar archive file.

chown thor:thor ...: Gives user thor complete ownership of the created backup file.

9. Script Execution Permissions
## 📌 Task Overview

Grant executable rights to a system maintenance script.

## 🎯 Why It Matters

Linux files created manually lack execution permissions (+x) by default for security reasons.

## 💻 Commands to Run
Bash
sudo chmod +x /opt/maintenance.sh
ls -l /opt/maintenance.sh
## 📖 Quick Breakdown
chmod +x /opt/maintenance.sh: Adds execution flag (x) to the target script file.

ls -l ...: Displays file permissions to confirm executable status (-rwxr-xr-x).

10. File Permission Correction
## 📌 Task Overview

Update permissions across web directories (755) and files (644) in bulk.

## 🎯 Why It Matters

Prevents web server vulnerabilities caused by overly permissive executable file settings.

## 💻 Commands to Run
Bash
sudo find /var/www/html -type d -exec chmod 755 {} +
sudo find /var/www/html -type f -exec chmod 644 {} +
## 📖 Quick Breakdown
find ... -type d -exec chmod 755 {} +: Recursively sets folder permissions to 755 (Read/Write/Execute for owner).

find ... -type f -exec chmod 644 {} +: Recursively sets file permissions to 644 (Read/Write for owner, Read-only for others).

11. String Replacement Across Configurations
## 📌 Task Overview

Search and replace hostname strings in configuration files using sed.

## 🎯 Why It Matters

Fast and accurate way to bulk update configuration files without manual editing mistakes.

## 💻 Commands to Run
Bash
sudo sed -i 's/stapp01.local/stapp01.stratos.local/g' /etc/hosts
## 📖 Quick Breakdown
sed -i ...: Edits file in-place and replaces all occurrences of stapp01.local with stapp01.stratos.local.

12. Secure Data Transfer Across Hosts
## 📌 Task Overview

Transfer application directories from jump host to App Server using scp.

## 🎯 Why It Matters

Secure Copy Protocol (scp) encrypts file transfers across server networks over SSH.

## 💻 Commands to Run
Bash
scp -r /home/thor/beta /home/thor/cluster steve@stapp02:/tmp/
## 📖 Quick Breakdown
scp -r ...: Recursively copies listed directories from local machine to remote path (/tmp/) on stapp02.

13. Restrict Cron Access
## 📌 Task Overview

Deny specific users from creating scheduled automated jobs via crontab.

## 🎯 Why It Matters

Prevents unapproved users from executing resource-heavy background tasks on servers.

## 💻 Commands to Run
Bash
echo "restricted_user" | sudo tee -a /etc/cron.deny
## 📖 Quick Breakdown
echo ... | tee -a /etc/cron.deny: Appends username to /etc/cron.deny file to block crontab access.

14. Default GUI / CLI Boot Configuration
## 📌 Task Overview

Change systemd default boot target to multi-user mode (CLI) instead of GUI.

## 🎯 Why It Matters

Disabling graphical interfaces on servers saves CPU/RAM and boosts performance.

## 💻 Commands to Run
Bash
sudo systemctl set-default multi-user.target
sudo systemctl get-default
## 📖 Quick Breakdown
systemctl set-default multi-user.target: Configures server to boot into command-line interface.

systemctl get-default: Displays current active boot target to verify.

15. Timezone Alignment
## 📌 Task Overview

Synchronize system clock and set overall server timezone to UTC.

## 🎯 Why It Matters

Keeps log files consistent across distributed server clusters for easy troubleshooting.

## 💻 Commands to Run
Bash
sudo timedatectl set-timezone UTC
timedatectl status
## 📖 Quick Breakdown
timedatectl set-timezone UTC: Sets global system timezone to UTC.

timedatectl status: Displays system time settings to verify alignment.

16. Firewall Port Configuration
## 📌 Task Overview

Open custom application ports (6400/tcp) in firewalld permanently.

## 🎯 Why It Matters

Opens network access for custom web applications while maintaining general firewall protection.

## 💻 Commands to Run
Bash
sudo systemctl start firewalld
sudo firewall-cmd --permanent --add-port=6400/tcp
sudo firewall-cmd --reload
## 📖 Quick Breakdown
firewall-cmd --permanent --add-port=6400/tcp: Saves rule to open TCP port 6400 permanently.

firewall-cmd --reload: Reloads firewall rules without interrupting active connections.

17. Process Limit Adjustment
## 📌 Task Overview

Increase user maximum process limits (nproc) in /etc/security/limits.conf.

## 🎯 Why It Matters

Prevents process bottlenecks and "Resource temporarily unavailable" errors under heavy web load.

## 💻 Commands to Run
Bash
echo "* soft nproc 4096" | sudo tee -a /etc/security/limits.conf
echo "* hard nproc 4096" | sudo tee -a /etc/security/limits.conf
## 📖 Quick Breakdown
soft nproc 4096: Sets initial warning threshold for user process limit.

hard nproc 4096: Sets absolute maximum cap for user process limit.

18. SELinux Installation and Configuration
## 📌 Task Overview

Install SELinux management tools and permanently disable SELinux in configuration.

## 🎯 Why It Matters

Prepares servers for custom application setups where mandatory access controls are managed at higher levels.

## 💻 Commands to Run
Bash
sudo dnf install -y selinux-policy selinux-policy-targeted policycoreutils
sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
grep "^SELINUX=" /etc/selinux/config
## 📖 Quick Breakdown
dnf install ...: Installs required core packages and tools for SELinux management.

sed -i ...: Sets SELINUX=disabled inside /etc/selinux/config for subsequent reboots.

grep ...: Checks the configuration file to confirm changes.