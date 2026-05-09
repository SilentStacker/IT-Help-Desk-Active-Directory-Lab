# 02 - osTicket Help Desk Setup

**Author:** Jamari James  
**Environment:** Fedora Linux · Apache · PHP · MariaDB · osTicket v1.18.3

---

## 📌 Overview
Deployed a fully functional IT help desk ticketing system using
osTicket on a Fedora Linux LAMP stack. This simulates a real-world
help desk environment for ticket intake, management, and resolution.

---

## 🧱 Stack

| Component | Details |
|---|---|
| OS | Fedora Linux |
| Web Server | Apache HTTP Server |
| Language | PHP |
| Database | MariaDB |
| Ticketing System | osTicket v1.18.3 |

---

## ⚙️ Installation Steps

### 1. Install LAMP Stack
```bash
sudo dnf install httpd php php-mysqlnd php-gd php-imap \
php-mbstring php-xml mariadb-server -y

sudo systemctl enable --now httpd
sudo systemctl enable --now mariadb
```

### 2. Secure MariaDB
```bash
sudo mysql_secure_installation
```

### 3. Create Database and User
```sql
CREATE DATABASE osticket;
CREATE USER 'osticket'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticket'@'localhost';
FLUSH PRIVILEGES;
```

### 4. Download and Place osTicket Files
```bash
sudo mv upload /var/www/html/osticket
sudo chown -R apache:apache /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
```

### 5. Configure SELinux
```bash
sudo semanage fcontext -a -t httpd_sys_rw_content_t \
"/var/www/html/osticket(/.*)?"
sudo restorecon -Rv /var/www/html/osticket
```

### 6. Complete Web Installer
- Navigated to `http://localhost/osticket/setup`
- Entered database credentials
- Created admin account
- Removed setup directory after install

---

## 🔥 Issues Encountered & Resolved

| Issue | Cause | Resolution |
|---|---|---|
| HTTP 403 Forbidden | File permissions + SELinux | Fixed permissions and SELinux context |
| HTTP 404 Not Found | Wrong file path | Moved files to correct directory |
| HTTP 500 Server Error | Missing PHP modules | Installed required PHP extensions |
| MySQL 1045 Access Denied | Wrong DB credentials | Corrected MariaDB user and password |
| IMAP PHP module missing | Module not installed | Installed php-imap package |
| Config file not writable | Restrictive permissions | Adjusted ost-config.php permissions |

👉 [View Full Troubleshooting Log](../troubleshooting-log.md)

---

## 📸 Screenshots

![403 Permissions Error](screenshots/403-permissions-error.png)
![404 Path Error](screenshots/404-path-error.png)
![HTTP 500 Error](screenshots/http-500-error.png)
![MySQL 1045 Error](screenshots/mysql-1045-error.png)
![IMAP PHP Issue](screenshots/imap-php.png)


---

## ✅ Outcome
Successfully deployed osTicket and configured a fully functional
help desk system. Resolved 6 real installation errors through
structured troubleshooting and documentation.
