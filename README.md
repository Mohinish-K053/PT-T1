
# Ansible Setup: MySQL, PostgreSQL, and NGINX with Public Access

This project automates the installation and configuration of **MySQL**, **PostgreSQL**, and **NGINX** using **Ansible** on an Ubuntu server. It ensures all services are accessible remotely for development or testing purposes.

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

```bash
mkdir <folder_name>
cd <folder_name>
vi install.yml
```

### Paste the following in `install.yml`:

```yaml
---
- name: Install MySQL, PostgreSQL, and NGINX
  hosts: localhost
  become: yes
  connection: local

  tasks:
    - name: Update apt
      apt:
        update_cache: yes

    - name: Install MySQL
      apt:
        name: mysql-server
        state: present

    - name: Configure MySQL to listen on all interfaces
      lineinfile:
        path: /etc/mysql/mysql.conf.d/mysqld.cnf
        regexp: '^bind-address'
        line: 'bind-address = 0.0.0.0'

    - name: Restart MySQL
      service:
        name: mysql
        state: restarted

    - name: Install PostgreSQL
      apt:
        name: postgresql
        state: present

    - name: Set PostgreSQL to listen on all addresses
      lineinfile:
        path: /etc/postgresql/*/main/postgresql.conf
        regexp: '^#?listen_addresses'
        line: "listen_addresses = '*'"

    - name: Allow external connections to PostgreSQL
      blockinfile:
        path: /etc/postgresql/*/main/pg_hba.conf
        block: |
          host    all             all             0.0.0.0/0               md5

    - name: Restart PostgreSQL
      service:
        name: postgresql
        state: restarted

    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Start and enable NGINX
      service:
        name: nginx
        state: started
        enabled: yes
```

---

## 🚀 Run the Ansible Playbook

```bash
ansible-playbook install.yml
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

