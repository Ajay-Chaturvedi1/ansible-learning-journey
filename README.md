Ansible Introduction:-
Ansible is an open-source, agentless automation tool used for configuration management, application deployment, and infrastructure automation. 
It uses SSH for communication and YAML-based playbooks to define tasks, making automation simple, scalable, and easy to maintain.
Key Features
Agentless Architecture – No software needs to be installed on target servers. Ansible uses SSH (Linux/Unix) or WinRM (Windows) to connect.
Simple YAML Syntax – Automation tasks are written in easy-to-read YAML files called Playbooks.
Idempotent – Running the same playbook multiple times produces the same result without causing unwanted changes.
Scalable – Can manage a few servers or thousands of servers simultaneously.
Cross-Platform Support – Works with Linux, Windows, cloud platforms, containers, and network devices

Core Components
Control Node – The machine where Ansible is installed and from which automation is executed.
Managed Nodes – Target servers/devices managed by Ansible.
Inventory – A file containing the list of managed hosts.
Modules – Prebuilt units of work (e.g., package installation, service management).
Playbooks – YAML files that define automation tasks.
Roles – A structured way to organize playbooks and reusable configurations.

Example Playbook
---
- name: Install Apache Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start Apache service
      service:
        name: httpd
        state: started
        enabled: yes
      
Benefits of Ansible
Reduces manual effort and human errors.
Ensures consistent server configurations.
Speeds up deployments and infrastructure management.
Easy to learn compared to many automation tools.
Common Use Cases
Configuration Management
Application Deployment
Infrastructure as Code (IaC)
Cloud Provisioning
Security and Compliance Automation
Patch Management

Interview Introduction (Short Answer):

"Ansible is an open-source, agentless automation tool used for configuration management, application deployment, and infrastructure automation. 
It uses SSH for communication and YAML-based playbooks to define tasks, making automation simple, scalable, and easy to maintain."


## Ansible Advance Automation Features:-
### Roles
- Roles simplifies long playbooks by grouping tasks into smaller playbooks.
OR
The Roles are the way of breaking a playbook into multiple playbook files. This simplifies writing complex playbooks and it makes them easier to reuse
- Writing ansible code to manage the same services for multiple environments  create more complexity and it becomes difficult to manage everything in one ansible playbook. Also sharing code among other teams become difficult. That is where Ansible Role helps solve these problems.
- Roles are like templates that are most of the time static and can be called by the playbooks.
- roles allow the entire configuration to be grouped in:
- - Tasks
  - modules
  - Variables
  - Handleers 
<img width="1356" height="744" alt="image" src="https://github.com/user-attachments/assets/956ab770-5aa7-463e-9dcc-44d48f0fec4a" />
> in order to better understanding of roles let's take an example here is a list of tasks that you want to performm on east webservers. first you need to install apache webserver and in second task you need to start the service and in third task you need to do it's entry in to firewalld and allow the service on the firewall.
> Now i have another list of task for our west web servers and for our west webservers we need to perform only two tasks first install httpd packages and second start the httpd service.
> All right , so this is what i wanted to do out and wanted to do with one playbook. then you will it has a list of 6 task. which is difficult to manage and complex
> So let's say if you have another group of remote clients, let's call it north webservers. and in that you only want to sart a webservice call so you need to mention a new task call seventh task
> so this things make your playbook a lot longer, and ofcource it 's hard to manage as well that's we will use the roles to make our playbook look a lot neate, cleaner, organized and easy to use.

#Screenshot

- so how are we going to transform them into different roles?
> So we will make a role playbook out of this
> so the playbook will be the same as it as we would write any playbook, but we will put them in the roles directory. So it will have setup server as a name and the task will be install a httpd package and start and enable httpd service enable the http port in firewall and restart the firewall.

```yaml
---
- name: Setup full httpd webserver
  tasks:
  - name: Install httpd packages
    yum:
      name: httpd
      state: present
  - name: Start httpd service
    service:
      name: httpd
      state: strated
  - name: enabled httpd service
    firewalld:
      name: http
      permanent: true
      state: enabled
  - name: Restart firewalld
    service:
      name: firewalld
      state: restarted
```
> and for another part of this playbook for only install httpd packages and start and enable the service we create an another role.
```yaml
---
- name: Setup full httpd webserver
  tasks:
  - name: Install httpd packages
    yum:
      name: httpd
      state: present
  - name: Start httpd service
    service:
      name: httpd
      state: strated
```
>Once we have these roles created, then we could write our final playbook and this is very simplified version of playbook

```yml
---
- name: Full install
  hosts: east-webservers
  roles:
  - fullinstall
- name: Basic install
  hosts: west-webserver
  roles:
  - basicinstall
```
- Please note roles can be grouped by type of servers, type of applications or organizational requirement.
- To crate roles.
- - Go to ControlNodes
  - cd /etc/ansible/roles
  - Make directory for each role
  - - e.g mkdir [rolenames]
    - mkdir basicinstall
    - mkdir fullinstall
    - mkdir appservers
    - - Create sub-directory tasks within each directory
      - mkdir basicinstall/tasks
      - mkdir fullinstall/tasks
      - mkdir appservers/tasks
      - - Create yml files within these sub-directories
        - touch basicinstall/tasks/main.yml
        - touch fullinstall/tasks/main.yml
        - touch appservers/tasks/main.yml
        - - vim basicinstall/tasks/main.yml
          - vim fullinstall/tasks/main.yml
```yml
---
- name: Setup full httpd webserver
  tasks:
  - name: Install httpd packages
    yum:
      name: httpd
      state: present
  - name: Start httpd service
    service:
      name: httpd
      state: strated
```
      
```yml
---
- name: Setup full httpd webserver
  tasks:
  - name: Install httpd packages
    yum:
      name: httpd
      state: present
  - name: Start httpd service
    service:
      name: httpd
      state: strated
  - name: enabled httpd service
    firewalld:
      name: http
      permanent: true
      state: enabled
  - name: Restart firewalld
    service:
      name: firewalld
      state: restarted
```
> in Playbook directory we crate a playbook that name well be byrole.yml , in this playbook we use that role which we have just created

- vim /etc/ansible/playbooks/byrole.yml
```yml
---
- name: Full install
  hosts: all
  roles:
  - fullinstall

- name: Basic install
  hosts: localhosts
  roles:
  - basicinstall         
```

# ROLES BY APPLICATION
```yml
---
- name: Setup full httpd webserver             
  tasks:                                                         
  - name: Install httpd packages                  ───────────────────│
    yum:                                                             │
      name: httpd                                                    ├─────  this is first task that would install packages of httpd
      state: present                              ───────────────────│
  - name: Start TIme Packages                     ───────────────────│
    yum:                                                             │
      name: ntpd  or chrony                                          ├───── this is second task will install time packages 
      state: present                              ───────────────────│

 - name: Start DNS service                        ───────────────────│
    yum:                                                             │
      name: named                                                    ├───── this is second task will install time packages 
      state: present                              ───────────────────│
```
---
for this what could we do 
- To create roles
- - Go to ContralNode
  - cd /etc/ansible/roles
- Make directory for each role
- - mkdir apache
  - mkdir ntpd
  - mkdir named
- Create sub-directory tasks within each directory
- - mkdir apache/tasks
  - mkdir ntpd/tasks
  - mkdir named/tasks
- Create yml files within these sub-directories
- - touch apache/tasks/main.yml
  - touch ntpd/tasks/main.yml
  - touch named/tasks/main.yml

> once we have done that simply write our simple short and sweet playbook and that would be defining of course the YAML formal.
```
---
- name: Install Packages
  hosts: all
  roles:
  - apache
  - ntpd
  - named
```

## Roles on Ansible Galaxy
- You can find a ton of resources on roles through Ansible galaxy.
- You can download pre-defined or pre-written roles from the Ansible galaxy.
- [https://galaxy.ansible.com/ui/](https://galaxy.ansible.com/ui/)

# Ansible Tags
- Tags are the reference or aliases to a task in a playbook
- Instead of running an entire Ansible playbook, use tags to target a specific tasks you need to run.
```
# vim httpdbytags.yml
---
- name: Setup Apache server
  hosts: all
  tasks:
  - name: Install httpd
    yum:
      name: httpd
      state: present
    tags: i-httpd
- name: Start httpd
  service:
    name: httpd
    state: started
  tags: s-httpd
```
- To run the perticular task using tags
```
 ansible-playbook httpdbytags.yml -t i-httpd
```
```
 ansible-playbook httpdbytags.yml -t s-httpd
```
- To list tags in a playbook
```
# ansible-playbook httpdbytags.yml --list-tags
```
- To skip a task using tag
```
# ansible-playbook httpbytags.yml --skip-tags i-httpd
```
- We can use "task option" to start a playbook at a specific task
```
# ansible-playbook yamlfile.yml --start-at-task 'Task name'
# absible-playbook httpdbytags.yml --start-at-task 'Install httpd'
```
# Variables
- Variables like containers that hold the defined value which can be used repetitively.
### IMPORTANT THINGS TO REMEMBER ABOUT VARIABLES!
- Name can include letters, numbers and underscore
- Name should always start with a letter
- Cannot have a spaces, dots(.) or hypen(-) in variable name
- Variable can be defined inside of inventory files as well

### Example:-
```
---
- name: Install some package
  hosts: all
  vars:
    sespackage: sesquipedalianism

  tasks:
  - name: Package install
    yum:
      name: "{{ sespackage }}"
      state: present

  - name: Start service
    service:
      name: "{{ sespackage }}"
      state: started
```

## Variables in Inventory Files:-
```
# Ansible Inventory File in INI Format

[webservers]
# Host variables defined directly next to the server names
://example.com ansible_host=192.168.1.10 http_port=80
://example.com ansible_host=192.168.1.11 http_port=8080

[dbservers]
://example.com ansible_host=192.168.1.20 max_connections=100

[all:vars]
# Global variables that apply to every single host in this inventory
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
ntp_server=pool.ntp.org

```
```
---
# Ansible Inventory File in YAML Format
all:
  hosts:
    # Global hosts can go here if they do not belong to a specific group
  children:
    webservers:
      hosts:
        ://example.com:
          ansible_host: 192.168.1.10
          http_port: 80
        ://example.com:
          ansible_host: 192.168.1.11
          http_port: 8080
    dbservers:
      hosts:
        ://example.com:
          ansible_host: 192.168.1.20
          max_connections: 100
  vars:
    # Global variables that apply to all groups
    ansible_user: ubuntu
    ansible_ssh_private_key_file: ~/.ssh/id_rsa
    ntp_server: pool.ntp.org
...
```

# Handlers:-

















# Ansible Vault:- 
- Ansible can automate tasks for teams such as:
- - Hardware
  - Operating Systems
  - Virtualization
  - Database or Storage
  - Applications/Software etc.

- Oftentimes you have to share Ansible code with these groups over the network and anything you share over network has a risk to end up in wrong hands.
- It is best practice to use Ansible vault feature which will password protect your code.
- yaml file with ansible-vault
```
ansible-vault create httpbyvault.yml
```
> It will ask about password, provide the password and confirm that then after you will enter in VI editor write your playbook and save file.
```
---
- name: Install httpd package
  hosts: localhost

  tasks:
  - name: Install package
    yum:
        name: httpd
        state: present
```
- If you run ansible-vault playbook as we run another playbooks it will show an error.
```
ansible-playbook httpbyvault.yml = ERROR!
```

- To run vault yaml file
```
ansible-playbook httpbyvault.yml --ask-vault-pass
```

- You can not see vault file by using simple cat command, If you want to view vault yaml file, 
```
ansible-vault view httpbyvault.yml
```

- To edit an existing vaulted yaml file.
  ```
  ansible-vault edit httpbyvault.yml
  ```

- To get a list of options.
```
ansible-vault --help
```

- httpbyvault.yaml file was created with ansible-vault, what about exiting files???
> To convert simple playbooks into vault playbooks use this command.
```
vim testbyvault.yml
```
```
ansible-vault encrypt testbyvault.yml
```

- Strings/words can be encrypted within a playbook.
```
ansible-vault encrypt_string httpd
```
```
ansible-vault create/encrypt outputbystring.yml
```
---

screenshot
---

# Ansible Management Tools
- Ansible management tool provides centralized web interface to manage your playbooks, inventories and everything you do through a command line.
- These tools also manage multiple Ansible control nodes
- These are 2 Ansible Management tools.
- - Ansible AWX (Free)
  - Ansible Tower (Lincensed product)

## Ansible AWX 
- AWX stands for Ansible WorkX
- AWX is a web application that provides a user interface, REST API, and task engine for Ansible
- The AWX allows you to manage Ansible playbooks, inventories, run reports and schedule jobs.
- Ansible AWX is an open-source, community driven management tool.
- It is the upstream product, meaning all the chages are done on AWX first before it get to Ansible Tower.
- The AWX Project is hosted on GitHuband RedHat welcomes community contributions.
- The AWX project uses GitHub for its issue tracking as well. You can file your issues here:
- - [https://github.com/ansible/awx/issues](https://github.com/ansible/awx/issues)
  - RedHat does **NOT** recommend **AWX** for production environment.
 
### Pros:-
- Full enterprise features and functionality of Tower.
- This is available for free download and use.
- Not limited the number of nodes to be added.
- Great for POC/dev/lab or QA environments.

### Cons:-
- No technical support by RedHat.
- Multiple release in a single day are possible.
- Not recommended by RedHat to be used in production environment.

## Ansible AWX Install (Please note: AWX is deployed from the docker containers)
- Update the system
```
yum update -y
```

- Reboot
```
reboot
```

- Install epel-release repository
 ```
yum install epel-release
 ```

- Install additional packages
```
dnf install git gcc gcc-c++ ansible nodejs gettext device-mapper-persistent-data lvm2 bzip2 python3-pip
```

- Add docker repository
```
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

- Install Docker
```
dnf install docker-ce --nobest -y
```
```
dnf install docker-ce --nobest --allowerasing
```

- Check Docker version
```
docker --version
```

- start docker
```
systemctl start docker
```

- Enable docker at boot
```
systemctl enable docker
```

- Install docker-compose
```
pip3 install docker-compose
```

- Verify the docker-compose version
```
docker-compose --version
```

- Set python command to use python3
```
alternatives --set python /usr/bin/python3
```

- Download the latest version of Ansible AWX from the Git Hub repositoy
```
git clone https://github.com/ansible/awx
```

- Generate a secret key for encryption fo the inventory file and copy the output to a text file.
```
openssl rand -base64 30
```

- Change directory to and edit inventory file
```
awx/installer
or
awx/tools/docker-compose

vim inventory
```
> in this file you suppose to change secret_key with that key what you get when you was run openssl command
- Add or modify the following parameters
- create directory for postgress
```
mkdir /var/lib/pgdocker
```

- Install AWX
```
ansible-playbook -i inventory install.yml
```

> when you have completed AWX installation , open any browser and put that servers ip on AWX installed then a desbord will opened, So from anything you do from command line you can do from dashboard.

# Ansible Tower:-
- Ansible Tower is a web-based dashboard and enterprise management hub for Ansible, designed to centralize and control IT automation across an entire organization. It provides a visual interface, role-based access control (RBAC), job scheduling, and graphical inventory management to help teams scale their Ansible playbooks securely.
> (Note: Red Hat has transitioned the commercial downstream version of Ansible Tower into the Red Hat Ansible Automation Platform, while its upstream open-source project is known as AWX).

- Pros
- - 24hrs full technical support
  - Release cycles
  - Fully tested for quality and engineering issues before release
  - Install/upgrades are well-documented and supported

- Cons:
- - Expensive
  - You may need more than 10 nodes for a dev/POC environment.
  - May be overkill for what you are trying to do.

# Ansible Additional Commands:-
- Ansible software comes with additional resources and we can utilize those resources through built-in commands comes with Ansible software.
- Following are the list of Additional Ansible commands :
- - ansible-config
  - - Shows or modify Ansible configuration
    - Not all configuration options are present in the command line, just the ones deemed most useful or common. Settings in the command line will override those passed through the configuration file and the environment.
  - ansible-connection
  - - Connection command for the remote clients.
  - ansible-console
  - - Allows fo rrunning ad-hoc tasks against a chosen invetory from a nice shell with built-in tab completion
    - It supports serveral commands, and you can modify its configuration at runtime
    - You can run name of the listed command followed by help
    - - E.g. # help dnf
    - To copy a file on the remote client = # cp /tmp/ans5 /home/iafzal
    - - > this command will run of client machine and copy from that /tmp directory to /home directory
    - Certain commands are misleading e.g = cd which changes the hosts instead of changing the diretory
  - ansible-doc
  - - You can access manuals on plug-ins and modules through this command
    - # ansible-doc -l (To list all moudles)
  - ansible-galaxy
  - - command to manage Ansible roles in shared repositories, the default of which is Ansible Galaxy [https://galaxy.ansible.com](https://galaxy.ansible.com)
> just type **ansible** on terminal this all commands will be showing there
 - ansible-inventory
 - - Using the ansible-invetory command provides you with details of your host invetory files
   - It can be useful if you want to get an idea of how your hosts are grouped. For example, run the following:
   - ```
     ansible-inventory -i hosts --graph 
     ```
   - To get a full list of hosts in hosts file
   - ```
     ansible-inventory --list
     ```
  - **ansible-pull**
  - A mode called 'ansible-pull' can also invert the system and have systems 'phone home' via scheduled git checkouts to pull configuration directives from a central repository

# Ansible Documentation:-
[https://docs.ansible.com/](https://docs.ansible.com/)
[https://docs.ansible.com/projects/ansible/latest/](https://docs.ansible.com/projects/ansible/latest/)
[www.ansible.com/resources](www.ansible.com/resources)
