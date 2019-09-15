### LAB1 - Ansible Control Machine and Managed Nodes
---

### Goal
In this lab, you will learn the basic concepts of Ansible.

### Basic Concepts
A control machine is any computer or virtual machine where Ansible is installed to run commands and playbooks.

Managed nodes are machines or hosts that the control macnine connect to and to make configuration changes or to install software.

Inventory is a file (/etc/ansible/hosts) listing or grouping one or more managed nodes or hosts.

Configuration file (ansible.cfg) let you specify or change the default Ansible settings and behaviors. 

Playbooks are files that you define the configurations and tasks to play in the managed hosts.

Ansible works by connecting to the managed nodes from a control machine, and pushing out small programs, called "modules" defined in the playbook to them. Ansible then executes these modules over SSH by default, and removes them when finished. While passwords are supported, but SSH keys are the preferred and secured way to use Ansible.

### Exercises

1. Login to an EC2 control machine using a Web terminal (Jupyter)
2. Create a default inventory and configuration file
3. Run a playbook to install and configure a LAMP stack


Ansible represents machines it manages using a file (INI or YAML formatted) to put all of machines, or put them in groups.

```
[consoles]
console[1:20].missionpeaktechnologies.com


[runners]
runner[1:20].lab.mpt.local
```





