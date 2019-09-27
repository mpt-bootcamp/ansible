## LAB3 - SSH Basic and Privilege Escalation
---

### SSH Basic

Ansible uses native OpenSSH for remote communication and SSH keys for authentication. Although password authentication is supported, it is not recommended for security best practice. Also using password is not convenient when playbooks have to be run non-interactive mode.

To connect from the control machine to  a managed node, Ansible piggyback-ride on SSH connectivity between those two machines. To run a playbook on the managed node all the basic setup needed for SSH connectivity is a prerequisite. 

When using SSH keys for authenticatin, Ansible uses the given user and private key in the command-line interface (CLI). The public key should be located in **~remote_user/.ssh/authorized_keys** on remote systems. The private key is stored on the control machine, typically in the **~local_user/.ssh/** directory. Also, the remote user given is a ***sudoer*** user, meaning it has admin privilege to execute administrative commands on the managed node. For example,


```console
# Linux sudo command restart the Apache service
$ sudo systemctl restart apapche2
```

If you don't specify the local user and priviate key to use, Ansible assumes the current user (**$USER**) and private key (**~/.ssh/id_rsa**).


### Privilege Escalation

When executing configuration tasks, it often requires to escalate the user priviliege to ***root*** or ***admin*** via **sudo**. You can tell Ansible to run privilege tasks by specifying the ***become*** and ***become_user*** parameters. If ***become-user*** is omitted, ***root*** is assumed. Also Ansible expects the user executing the privilege tasks is setup to use passwordless (NOPASS) in the ***/etc/sudoers*** file or ***/etc/sudoers.d/*** directory. For example,

In the comand-line,

```
$ ansible ... --become --become-user=root ... -m <module> -a <arguments>
```

In a playbook,
```
# Tell Ansible to become the root user to execute all host commands.
- hosts: all
  become: yes
  become_user: root
```

### Exercise 1 - Generate a SSH key pair

In this exercise an SSH keypair is generated for setting up passwordless access between the control machine and managed node.

```console 
$ mkdir -p ~/.ssh
$ cd ~/.ssh
$ ssh-keygen -t rsa -N "" -f id_rsa
$ ls -ltr 
```
id_rsa is the private key and id_rsa.pub is the public key. 

### Exercise 2 - Copy the public key to the managed node using Ansible CLI

The content of id_rsa.pub is added to ~remote_user/.ssh/authorized_key on the managed node to allow a user from control machine to connect to managed node using this key pair.

```console
# Ping the if the remote host is reachable by Ansible
$ ansible runner<n>.lab.mpt.local -m ping -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu

# Use Ansible authorized_key module to copy the SSH public key to the remote host
$ ansible runner<n>.lab.mpt.local --become -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m authorized_key -a "user=$USER state=present key={{lookup('file', '~/.ssh/id_rsa.pub')}} manage_dir=yes"

```

### Exercise 3 - Login to the managed node using SSH connection

Now test the SSH connection using the SSH key pair

```console
$ ssh $USER@runner<n>.lab.mpt.local
```

Show the sudoers setting for runner<n> on managed node.
TBD

### Exercise 4 - Run a playbook passwordless as runner user

$ ansible-playbook --private-key=~/.ssh/id_rsa -u runner<n> playbook.yml
TBD

---
## LAB3 - End

