# 🛠️ Troubleshooting Log – osTicket Help Desk Deployment

## 📌 Overview

This document outlines the issues encountered during the deployment of an osTicket help desk system on a Fedora Linux environment using Apache, PHP, and MariaDB.
Each issue includes a description, root cause, and resolution to demonstrate a structured troubleshooting approach.

---

## 🔴 Issue 1: MariaDB Service Not Installed or Unavailable

**Description:**
MariaDB service could not be started.

**Error Message:**
`mariadb.service does not exist`

**Root Cause:**
MariaDB server package was not installed.

**Resolution:**
Installed and enabled MariaDB service:

```bash
sudo dnf install mariadb-server -y
sudo systemctl enable --now mariadb
```

---

## 🔴 Issue 2: osTicket Download Failure (HTTP 404)

**Description:**
Failed to download osTicket installation package.

**Error Message:**
HTTP 404 error and invalid ZIP file.

**Root Cause:**
Incorrect or outdated GitHub release URL.

**Resolution:**
Downloaded correct version and extracted files:

```bash
wget https://github.com/osTicket/osTicket/releases/download/v1.18.3/osTicket-v1.18.3.zip -O osticket.zip
unzip osticket.zip
```

---

## 🔴 Issue 3: Missing Application Files After Extraction

**Description:**
Required `upload/` directory was not present.

**Error Message:**
`mv: cannot stat 'upload': No such file or directory`

**Root Cause:**
Corrupted or incomplete extraction due to failed download.

**Resolution:**
Re-downloaded package and verified correct extraction.

---

## 🔴 Issue 4: Incorrect Web Root Directory (HTTP 404)

**Description:**
osTicket application not accessible via browser.

**Root Cause:**
Files were placed in an incorrect directory (`/var/www/ntml` instead of `/var/www/html`).

**Resolution:**
Moved files to correct Apache web root:

```bash
sudo mv upload /var/www/html/osticket
```

---

## 🔴 Issue 5: File Permissions and SELinux Restrictions (HTTP 403)

**Description:**
Access to osTicket directory was denied.

**Error Message:**
HTTP 403 Forbidden

**Root Cause:**
Incorrect file ownership, permissions, and SELinux context.

**Resolution:**
Adjusted permissions and SELinux settings:

```bash
sudo chown -R apache:apache /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
sudo restorecon -Rv /var/www/html/osticket
```

---

## 🔴 Issue 6: PHP Not Executing in Apache

**Description:**
Instead of loading the installer, the browser displayed a directory listing.

**Root Cause:**
PHP was not installed or properly configured with Apache.

**Resolution:**
Installed PHP and restarted Apache:

```bash
sudo dnf install php php-cli php-common -y
sudo systemctl restart httpd
```

---

## 🔴 Issue 7: Missing MySQLi PHP Extension

**Description:**
Installer reported missing MySQLi extension.

**Root Cause:**
Required PHP module not installed.

**Resolution:**
Installed MySQLi extension:

```bash
sudo dnf install php-mysqlnd -y
sudo systemctl restart httpd
```

---

## 🔴 Issue 8: Missing osTicket Configuration File

**Description:**
Installer could not locate `ost-config.php`.

**Root Cause:**
Configuration file was not created from the template.

**Resolution:**
Created configuration file:

```bash
sudo cp include/ost-sampleconfig.php include/ost-config.php
```

---

## 🔴 Issue 9: Configuration File Not Writable

**Description:**
Installer required write access to configuration file.

**Error Message:**
“Write access required to continue”

**Root Cause:**
Insufficient file permissions and SELinux restrictions.

**Resolution:**
Updated permissions and SELinux context:

```bash
sudo chown apache:apache /var/www/html/osticket/include/ost-config.php
sudo chmod 666 /var/www/html/osticket/include/ost-config.php
sudo restorecon -Rv /var/www/html/osticket
sudo chcon -t httpd_sys_rw_content_t /var/www/html/osticket/include/ost-config.php
```

---

## 🔴 Issue 10: Database Authentication Failure (MySQL Error 1045)

**Description:**
Unable to connect to MariaDB using application credentials.

**Error Message:**
`ERROR 1045 (28000): Access denied for user 'osticketuser'@'localhost'`

**Root Cause:**
Incorrect password or improperly configured database user permissions.

**Resolution:**
Recreated database user and reassigned privileges:

```sql
DROP USER IF EXISTS 'osticketuser'@'localhost';
CREATE USER 'osticketuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticketuser'@'localhost';
FLUSH PRIVILEGES;
```

---

## 🔴 Issue 11: Internal Server Error (HTTP 500)

**Description:**
Application failed after submitting installation form.

**Error Message:**
HTTP 500 Internal Server Error

**Root Cause:**
Database connection failure caused by incorrect credentials.

**Resolution:**

* Verified database connectivity
* Corrected credentials
* Restarted services:

```bash
sudo systemctl restart httpd mariadb
```

---

## ✅ Key Takeaways

* Developed a structured troubleshooting methodology
* Gained experience diagnosing HTTP errors (403, 404, 500)
* Improved understanding of Linux permissions and SELinux
* Strengthened knowledge of web application architecture (Apache, PHP, MariaDB)
* Practiced real-world IT problem-solving and documentation

---

## 📈 Summary

This troubleshooting process demonstrates the ability to deploy, diagnose, and resolve issues in a multi-component IT system.
The experience reflects real-world responsibilities of IT Support and System Administration roles, emphasizing both technical skill and problem-solving ability.

---

