# Troubleshooting Log – osTicket Installation

## Troubleshooting Method Used
For each issue, I followed a structured approach:
- Identified the error message
- Researched likely causes
- Applied targeted fixes
- Verified the solution before moving forward

---

## Issue: HTTP 403 Forbidden

### Problem
Accessing the osTicket setup page returned a 403 Forbidden error.

### Cause
Incorrect file permissions and/or SELinux restrictions preventing Apache from accessing the directory.

### Troubleshooting Steps
- Checked Apache service status
- Verified directory path and permissions
- Updated file ownership for web server access
- Reviewed SELinux context settings

### Resolution
Corrected file permissions and ensured Apache had proper access.

### Verification
Confirmed the osTicket setup page loaded successfully in the browser without a 403 error.

---

## Issue: HTTP 404 Not Found

### Problem
The osTicket page could not be found when accessing the URL.

### Cause
Incorrect file placement or misconfigured web server document root.

### Troubleshooting Steps
- Verified osTicket files were in the correct directory
- Checked Apache configuration
- Confirmed correct URL path

### Resolution
Moved files to the correct directory and corrected configuration.

### Verification
Confirmed the osTicket page loaded correctly when navigating to the URL.

---

## Issue: HTTP 500 Internal Server Error

### Problem
The server returned a 500 error when attempting to load osTicket.

### Cause
Server-side misconfiguration or missing dependencies.

### Troubleshooting Steps
- Checked Apache error logs
- Identified missing or misconfigured components
- Restarted Apache after adjustments

### Resolution
Resolved configuration issues and ensured required services were running.

### Verification
Confirmed the site loaded successfully without server errors.

---

## Issue: Missing PHP Modules

### Problem
osTicket installer reported missing required PHP extensions.

### Cause
Required PHP modules were not installed on the system.

### Troubleshooting Steps
- Reviewed osTicket system requirements
- Installed missing PHP modules (mysqli, gd, imap, etc.)
- Restarted Apache service

### Resolution
Installed all required PHP extensions.

### Verification
Confirmed all required modules passed in the osTicket installer check.

---

## Issue: Database Connection Failure

### Problem
osTicket could not connect to the MySQL/MariaDB database.

### Cause
Incorrect database credentials or database not properly configured.

### Troubleshooting Steps
- Verified database service was running
- Checked database name, username, and password
- Confirmed user permissions in MariaDB# 🛠️ Troubleshooting Log – osTicket Help Desk Deployment

**Description:**
Unable to connect to MariaDB using application credentials.

**Error Message:**
`ERROR 1045 (28000): Access denied for user 'osticketuser'@'localhost'`

**Root Cause:**
Incorrect password or improperly configured database user p# 🛠️ Troubleshooting Log – osTicket Help Desk Deployment

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
CREATE USER 'osticketuser'@'localhost' IDENTIFIED BY 'secure-password';
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

---ermissions.


```sql
DROP USER IF EXISTS 'osticketuser'@'localhost';
CREATE USER 'osticketuser'@'localhost' IDENTIFIED BY 'secure-password';
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

### Resolution
Corrected credentials and ensured proper database permissions.

### Verification
Confirmed successful database connection during osTicket installation.

---

## Issue: Configuration File Permissions

### Problem
The osTicket configuration file was not writable during setup.

### Cause
File permissions were too restrictive.

### Troubleshooting Steps
- Checked file permissions for ost-config.php
- Modified permissions to allow write access

### Resolution
Adjusted file permissions to allow installation to proceed.

### Verification
Confirmed the installer was able to write configuration settings successfully.

---

## Issue: Installation Form Not Submitting

### Problem
The osTicket installation form failed to submit.

### Cause
Likely due to missing dependencies, incorrect permissions, or configuration issues.

### Troubleshooting Steps
- Reviewed browser console for errors
- Checked PHP modules and configuration
- Verified file permissions and database connection

### Resolution
Resolved underlying configuration issues affecting form submission.

### Verification
Confirmed the installation form submitted successfully and proceeded to the next step.

---

