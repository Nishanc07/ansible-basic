# Create Ansible playbooks to install and configure Nginx, MySQL, and Docker Swarm.

This repository contains **Ansible roles** to install and configure Nginx, MySQL, and Docker Swarm on a Linux server (tested on Ubuntu 22.04).

---

## Prerequisites (I'm using my local machine as the controller and a VM as target machine)

1. Controller machine – your local machine where Ansible is installed.
2. Target machine (VM) – Ubuntu server (tested on 22.04) with SSH access.
3. Ansible installed on your local machine. You can check with: (ansible --version)
4. SSH key of the VM added to your local machine and ensure you can login(give executable permissions)
5. Python installed on the target VM (usually pre-installed on Ubuntu). Ansible requires it to run modules.
6. Firewall/Network access – your local machine can reach the VM on SSH port 22.

Steps---

1. First, create the directory structure using ansible-galaxy: Initialize roles using ansible-galaxy

```
mkdir ansible-infra-setup
cd ansible-infra-setup
ansible-galaxy init roles/nginx
ansible-galaxy init roles/mysql
ansible-galaxy init roles/docker_swarm
```

This will create the standard role structure for each service.

2. Create Inventory and Playbook Files

```
[myservers]
my_azure_vm ansible_host=<VM_IP> ansible_user=azureuser ansible_ssh_private_key_file=/path/to/your/key.pem
```

3. Populate Role Tasks
   For each role, edit roles/<role_name>/tasks/main.yml:
   Nginx – install, start, enable service, and create a basic index page.
   MySQL – install, start, enable service.
   Docker Swarm – install Docker, add user to docker group, and initialize swarm. (make sure you are adding vm user to docker group)

4. Run the Playbook

```
ansible-playbook -i inventory/hosts playbook.yml
```

After running, log out and back in on the VM so the docker group membership takes effect.

##Verification

1. Verify Nginx by opening http://<VM_IP> in a browser or

```
sudo systemctl status nginx
```

2. Verify Docker Swarm:

```
docker info | grep -i swarm
docker node ls
```

output: Swarm: active
ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS ENGINE VERSION
d8dp14rudajx93n3hgmnhknzh \* ansible Ready Active Leader 28.3.3

3. Verify mysql

```
sudo systemctl status mysql
```

Look for active (running) in the output.

![verification](https://github.com/Nishanc07/ansible-basic/blob/main/public/verification.png)

### Database setup

1. MySQL Setup

- Installed MySQL server using Ansible (apt module).
- Started and enabled the MySQL service.
- Installed the PyMySQL connector for Ansible compatibility.
- Created a MySQL database (mydb) and a user (mysql) with all privileges on that database.
- Verified connection to MySQL using:

```
mysql -u mysql -p -h localhost
```

2. PostgreSQL Setup

- Installed PostgreSQL packages (postgresql, postgresql-contrib, libpq-dev) via Ansible.
- Ensured PostgreSQL service is running and enabled.
- Set a password for the default postgres superuser safely.
- Created a PostgreSQL database (mypgdb) and a user (nisha) with login privileges.
- Granted all privileges on mypgdb to user nisha.
- Verified connection to PostgreSQL using:

```
psql -U nisha -d mypgdb -h localhost
```

Note: Using -h localhost forces password-based authentication instead of peer authentication.
