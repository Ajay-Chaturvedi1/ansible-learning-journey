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

