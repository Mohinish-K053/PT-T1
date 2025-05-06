
# Ansible Setup: MySQL, PostgreSQL, and NGINX with Public Access

This project automates the installation and configuration of **MySQL**, **PostgreSQL**, and **NGINX** using **Ansible** roles and inventory. It ensures all services are accessible remotely for development or testing purposes.

---

## 🔐 Security Group Configuration

Before proceeding, configure your **EC2 Security Group** to allow inbound traffic on the following ports:

| Port | Protocol | Description                  |
|------|----------|------------------------------|
| 22   | TCP      | SSH Access                   |
| 80   | TCP      | HTTP for NGINX               |
| 3306 | TCP      | MySQL remote access          |
| 5432 | TCP      | PostgreSQL remote access     |

---

## 🛠️ Install Ansible

Run the following commands on your EC2 instance:

```bash
sudo apt update -y
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

---

## 📁 Setup Project Folder

Unzip the provided `ansible-setup.zip` or clone your GitHub repo, then run:

```bash
cd ansible-setup
```

The structure is as follows:

```
ansible-setup/
├── inventory.ini
├── install.yml
└── roles/
    ├── common/
    ├── mysql/
    ├── postgresql/
    └── nginx/
```

---

## 🚀 Run the Ansible Playbook

```bash
ansible-playbook -i inventory.ini install.yml
```

---

## 🧩 Configure MySQL

```bash
sudo mysql
```

Inside the MySQL shell:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpassword';
CREATE USER 'root'@'%' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

---

## 🧩 Configure PostgreSQL

```bash
sudo -u postgres psql
```

Inside the PostgreSQL shell:

```sql
CREATE USER root WITH PASSWORD 'yourpassword';
ALTER ROLE root WITH SUPERUSER;
\q
```

---

## ✅ Verification Steps

1. **NGINX**  
   Visit your instance's public IP in a browser. You should see the default NGINX welcome page.

2. **MySQL**  
   Test remote access:
   ```bash
   mysql -h <public-ip> -u root -p
   ```

3. **PostgreSQL**  
   Test remote access:
   ```bash
   psql -h <public-ip> -U root -d postgres
   ```

When prompted, enter the password `yourpassword`.

---
