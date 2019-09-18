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

#### Download the deployment playbook

1. Login in to the Ansible control machine.

```console
mkdir -p ~/bootcamp
cd ~/bootcamp
git clone https://github.com/mpt-bootcamp/ansible.git
```

2. Create the inventory file for the lab environment

```console
cd ~/bootcamp/ansible/playbooks
mkdir -p inventories/lab
vi inventories/lab/hosts

# Add or paste the following lines, exit and save (ESC :wq)

[web]
runner1.lab.mpt.local
```

3. Create the inventory file for the dev environment

```console
cd ~/bootcamp/assets-manager/playbooks
mkdir -p inventories/dev
vi inventories/dev/hosts

# Add or paste the following lines, exit and save (ESC :wq)

[web]
runner19.lab.mpt.local
runner20.lab.mpt.local
```

4. Run the playbook to install the application to the ***lab*** environment

From the Ansible control machine,

```console
cd ~/bootcamp/assets-manager/playbooks
ansible-playbook -i inventory/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -v deploy-assets-manager.yml
```