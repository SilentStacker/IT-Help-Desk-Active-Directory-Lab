# 🛠️ Troubleshooting Log – osTicket Lab (Fedora Linux)

**Environment:** Fedora Linux · Apache · PHP · MariaDB · osTicket v1.18.3  
**Author:** Jamari James

---

## Troubleshooting Methodology

For every issue encountered, I followed a structured approach:

1. Identify the error message or symptom
2. Check relevant logs and service status
3. Research likely causes
4. Apply a targeted fix
5. Verify the resolution before moving forward

---

## Issue 1: MariaDB Service Not Found

### Symptom
```
mariadb.service does not exist
```

### Cause
The MariaDB server package was not installed on the system.

### Resolution
```bash
sudo dnf install mariadb-server -y
sudo systemctl enable --now mariadb
```

### Verification
Confirmed MariaDB was active and running:
```bash
sudo systemctl status mariadb
```

---

## Issue 2: osTicket Download Failure (HTTP 404)

### Symptom
`wget` returned a 404 error when attempting to download the osTicket package.

### Cause
The download URL was outdated or incorrect.

### Resolution
Used the correct GitHub release URL:
```bash
wget https://github.com/osTicket/osTicket/releases/download/v1.18.3/osTicket-v1.18.3.zip -O osticket.zip
unzip osticket.zip
```

### Verification
Confirmed the zip extracted successfully and the `upload/` directory was present.

---

## Issue 3: Missing `upload` Directory After Extraction

### Symptom
```
mv: cannot stat 'upload': No such file or directory
```

### Cause
The initial download was incomplete or corrupted, resulting in a failed extraction.

### Resolution
Re-downloaded the package using the correct URL and re-ran the extraction. Confirmed the `upload/` directory was present before proceeding.

---

## Issue 4: HTTP 404 Not Found (Browser)

### Symptom
Browser displayed `404 Not Found` when navigating to the osTicket URL.

![404 Error](02-osticket-setup/screenshots/404-path-error.png)


### Cause
The osTicket files were not placed in the correct Apache web root directory.

### Resolution
```bash
sudo mv upload /var/www/html/osticket
```

### Verification
Navigated to `http://localhost/osticket` and confirmed the osTicket installer loaded.

---

## Issue 5: HTTP 403 Forbidden

### Symptom
```
403 Forbidden
```

![403 Permissions Error](02-osticket-setup/screenshots/403-permissions-error.png)

### Cause
Incorrect file permissions and SELinux restrictions prevented Apache from accessing the osTicket directory.

### Resolution
```bash
sudo chown -R apache:apache /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
sudo restorecon -Rv /var/www/html/osticket
```

### Verification
Reloaded the browser and confirmed the osTicket installer was accessible without a 403 error.

---

## Issue 6: PHP Not Executing (Directory Listing Displayed)

### Symptom
Apache displayed a raw directory listing instead of running the osTicket installer.

### Cause
PHP was not installed or not properly configured with Apache.

### Resolution
```bash
sudo dnf install php php-cli php-common -y
sudo systemctl restart httpd
```

### Verification
Refreshed the browser and confirmed the PHP-based installer rendered correctly.

---

## Issue 7: Missing MySQLi PHP Extension

### Symptom
osTicket installer flagged a missing MySQLi extension, blocking installation.

### Cause
The `php-mysqlnd` package was not installed.

### Resolution
```bash
sudo dnf install php-mysqlnd -y
sudo systemctl restart httpd
```

### Verification
Reloaded the installer and confirmed MySQLi showed as passing in the requirements check.

---

## Issue 8: Missing Recommended PHP Extensions

### Symptom
osTicket installer flagged multiple missing extensions: GD, IMAP, Intl, APCu.

![IMAP PHP Error](02-osticket-setup/screenshots/imap-php.png)

### Cause
These optional but recommended PHP modules were not installed.

### Resolution
```bash
sudo dnf install php-gd php-intl php-pecl-apcu -y
sudo systemctl restart httpd
```

> **Note:** `php-imap` was unavailable in the enabled Fedora repositories (`No match for argument: php-imap`). Since IMAP is optional for osTicket, installation continued without it.

### Verification
Reloaded the installer and confirmed all available required extensions passed.

---

## Issue 9: Missing Configuration File

### Symptom
```
ost-config.php not found
```

### Cause
The configuration file had not been created from the provided sample template.

### Resolution
```bash
sudo cp /var/www/html/osticket/include/ost-sampleconfig.php \
        /var/www/html/osticket/include/ost-config.php
```

### Verification
Confirmed the file existed and the installer proceeded past this step.

---

## Issue 10: Configuration File Not Writable

### Symptom
```
Write access required to continue
```

### Cause
File permissions and SELinux context prevented Apache from writing to `ost-config.php`.

### Resolution
```bash
sudo chown apache:apache /var/www/html/osticket/include/ost-config.php
sudo chmod 666 /var/www/html/osticket/include/ost-config.php
sudo restorecon -Rv /var/www/html/osticket
sudo chcon -t httpd_sys_rw_content_t /var/www/html/osticket/include/ost-config.php
```

### Verification
Confirmed the installer accepted the file and moved to the next setup step.

---

## Issue 11: MySQL Authentication Failure (Error 1045)

### Symptom
```
ERROR 1045 (28000): Access denied for user 'osticketuser'@'localhost'
```

![MySQL 1045 Error](02-osticket-setup/screenshots/mysql-104-error.png)


### Cause
Incorrect database credentials or insufficient user permissions in MariaDB.

### Resolution
```bash
mysql -u root -p
```
```sql
ALTER USER 'osticketuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticketuser'@'localhost';
FLUSH PRIVILEGES;
```

### Verification
```bash
mysql -u osticketuser -p
```
Confirmed successful login and database access.

---

## Issue 12: HTTP 500 Internal Server Error

### Symptom
osTicket returned an `HTTP ERROR 500` after submitting the installation form. The installer loaded fine but failed on submission.

![HTTP 500 Error](02-osticket-setup/screenshots/http-500-error.png)


### Cause
A combination of factors:
- Database user authentication mismatch
- PHP errors suppressed by default configuration, hiding the real cause
- Application configuration issues triggered during installation

### Troubleshooting Steps
```bash
# Check service status
sudo systemctl status httpd
sudo systemctl status mariadb

# Test database login manually
mysql -u osticketuser -p

# Review Apache error logs
sudo tail -n 100 /var/log/httpd/error_log

# Enable PHP error display for debugging
sudo nano /etc/php.ini
# Set: display_errors = On

sudo systemctl restart httpd
```

### Resolution
- Reset and verified database credentials (see Issue 11)
- Confirmed all required PHP modules were installed
- Corrected file permissions and SELinux contexts
- Restarted services after all changes:

```bash
sudo systemctl restart httpd mariadb
```

### Verification
Resubmitted the installation form and confirmed osTicket installed successfully.

> **Key Lesson:** HTTP 500 is a symptom, not the root cause. Always check logs (`/var/log/httpd/error_log`) and enable PHP error display when debugging application errors.

---

## Issue 13: Git Push Rejected

### Symptom
```
Updates were rejected because the remote contains work that you do not have locally
```

### Cause
The GitHub repository had newer commits than the local environment (GitHub Codespaces).

### Resolution
```bash
git pull --rebase origin main
git push
```

### Verification
Confirmed push succeeded and changes were visible on GitHub.

---

## Issue 14: Changes Not Appearing on GitHub

### Symptom
Files were saved locally but changes were not visible on GitHub.

### Cause
Files were saved but never staged, committed, or pushed.

### Resolution
```bash
git add .
git commit -m "Updated troubleshooting log and documentation"
git push
```

### Verification
Confirmed changes appeared correctly on the GitHub repository.

---

## ✅ Final Result

Successfully deployed osTicket after resolving all 14 issues. Admin dashboard confirmed fully operational.

![Admin Dashboard](02-osticket-setup/screenshots/admin-dashboard.png)
---

## Key Lessons Learned

- **HTTP errors are symptoms** — always investigate logs to find the real root cause
- **SELinux** adds a security layer beyond standard Linux permissions; both must be correct for Apache to function
- **Service management** with `systemctl` is essential for diagnosing and recovering from failures
- **PHP modules** must be explicitly installed; a missing dependency can silently break an application
- **Database permissions** must be scoped correctly — a user needs the right privileges on the right database
- **Git workflow** (add → commit → push) must be completed fully for changes to appear on GitHub

---

## Real-World Skills Demonstrated

- Linux system administration (Fedora)
- Web server configuration (Apache/PHP)
- Database setup and troubleshooting (MariaDB/MySQL)
- File permissions and SELinux management
- Service management with `systemctl`
- Log analysis for root cause identification
- Git version control workflow
- Technical documentation and structured troubleshooting methodology