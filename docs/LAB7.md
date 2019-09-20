## LAB7 - Application Deployment

### Goal
In this lab, you will learn how to use Ansible playbook to deploy an application.

### Basic Deployment Steps

1. Identify the target environment and nodes
2. Download the application package
3. Unpackage and setup the application configuration properties
4. Setup the application to run as a service
5. Start the application


### Lab Exercises

#### Exercise 1 - Download the deployment playbook

Login in to the Ansible control machine.

```console
mkdir -p ~/bootcamp
cd ~/bootcamp
git clone https://github.com/mpt-bootcamp/ansible.git
```

#### Exercise 2 - Setup the inventory file for the lab environment

```console
cd ~/bootcamp/ansible/playbooks
mkdir -p inventories/lab
vi inventories/lab/hosts
```
Add or paste the following lines, exit and save (ESC :wq)
```
[web]
runner1.lab.mpt.local
```

#### Exercise 3 - Run the application deployment playbookto the ***lab*** environment

From the Ansible control machine,

```console
cd ~/bootcamp/ansible/playbooks
ansible-playbook -i inventory/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -v deploy-assets-manager.yml
```

#### Exercise 4 - Review the playbook and role

You can use the Jupyter web editor, or vi from the Terminal window to view the following files:

* deploy-assets-manager.yml
* roles/assets-manager/*
