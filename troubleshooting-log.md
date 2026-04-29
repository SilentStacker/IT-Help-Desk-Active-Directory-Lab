#  Troubleshooting Log – osTicket Deployment

## Overview
This document outlines issues encountered while deploying osTicket on Fedora Linux and the steps taken to resolve them.

---

## Issue 1: MariaDB Service Not Found

**Error:**  
mariadb.service does not exist

**Cause:**  
MariaDB server package was not installed.

**Resolution:**  
Installed MariaDB and started the service:
```bash
sudo dnf install mariadb-server -y
sudo systemctl enable --now mariadb
