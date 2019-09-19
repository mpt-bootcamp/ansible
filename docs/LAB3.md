## LAB3 - Inventory File and Command-line Interface (CLI)

### Goal
In this lab, you will learn how to use inventory file and group variables upon commands, modules and tasks in a playbook operate.

### Hosts, groups and variables 

The inventory file can list individual hosts or user-defined groups of hosts. it can be written in one of many formats - INI, YAML, or JSON. The default location for the inventory file is ***/etc/ansible/hosts***. You can also create project-specific inventory files in alternate locations. For example,

```
ansible/playbooks/inventories/
├── dev
│   ├── group_vars
│   │   └── all
│   └── hosts
└── local
    ├── group_vars
    └── hosts
```

You can use multiple inventory files at the same time and pull inventory from dynamic or cloud sources or different formats (YAML, INI, JSON). Ansible combines all of them and run Ansible plays/tasks against those hosts. The ***group_vars/all*** file is used to set variables that will be used for every host that Ansible is ran against.

You can pass the inventory file to Ansible using the ***-i*** or ***--inventory-file*** option followed by the path to the file. If you do not explicitly specify any inventory file to Ansible, it will take the default path from the ***host_file*** parameter of ***ansible.cfg***, which defaults to ***/etc/ansible/hosts***.

If you want to run your Ansible tasks against all of these hosts, then you can pass ***all*** to the hosts parameter while running the ansible-playbook or to the ansible command, or you can limit to the specific hosts with the ***--limit*** option for the the ansible-playbook command.


### Lab Exercises

For lab execerises, we will be using the existing *ansible* project.

```console
# Go to the playbooks directory
$ cd ~/bootcamp/ansible/playbooks
$ pwd
```

#### Exercise 1 - Create a local inventory file for the lab environment

```console
# Create the enviornment and group variables folder
$ mkdir -p inventories/lab/group_vars
````

Create the inventory file with the following contents (vi inventories/lab/hosts). Make sure you replace \<n\> with assigned student number(1-18).

**INI version - hosts**

```
[web]
runner<n>.lab.mpt.local

[db]
runner19.lab.mpt.local

[monitor]
runner20.lab.mpt.local

```

**YAML version - hosts.yml**
```
all:
  hosts:
    runner<n>.lab.mpt.local:
  children:
    web:
      hosts:
        runner19.lab.mpt.local:
    db:
      hosts:
        runner20.lab.mpt.local
```

Next create a group variables file for all hosts (vi inventories/lab/group_vars/all) with the following contents.

```
---
env_name: lab
env_description: Lab environment 

```

#### Exercise 2 - Running Ansible commands with the inventory file

```console
# Ping all hosts in the default inventory file /etc/ansible/hosts
$ ansible all -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m ping

# Ping all hosts in the lab environment
$ ansible all -i inventories/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m ping

# Get the host facts for the web group
$ ansible web -i inventories/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m setup
```

#### Exercise 3 - Running Ansible commands with dynamic hosts

You can run commands against hosts that are not defined in the inventory file by listing the hosts

```console
ansible ops01 -i "console18.lab.mpt.local, console20.lab.mpt.local,"  -m ping -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu
```

#### Exercise 4 - Running command without valid authentication
```console
$ ansible all -m ping
$ ansible all -u student<n> -m ping
$ ansible all -u ubuntu -m ping
```
