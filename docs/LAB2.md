## LAB2 - SSH Connection and Privilege Escalation
---

### Goal
In this lab, you will learn how Ansible communicates with managed nodes over SSH and how to use privilege escalation to execute administrative tasks.

### SSH Connection

Ansible uses native OpenSSH for remote communication and SSH keys for authentication. Although password authentication is supported, it is not recommended for security best practice. When the control machine connects to the managed nodes, it first establishes an agreed encryption to protect the communication session. Then it authenticates the given user and verify whether access to the server should be granted. 

When using SSH keys for authenticatin, Ansible uses the given user and private key in the command-line interface (CLI). The public key should be located in **~/.ssh/authorized_keys** on remote systems. The private key usually stores in the **~/.ssh** directory of the user home. Also, the user given is a ***sudo*** user, meaning it has the escalated privilege to execute administrative commands ( with the sudo prefix before the commands). For example,

```console
# Linux sudo command restart the Apache service
sudo systemctl restart apapche2
```

If you don't specify the user and priviate key to use, Ansible assumes the current user (**$USER**) and private key (**~/.ssh/id_rsa**).

You can also specify the user and private key to use in the inventory file. For instance

```
# /etc/ansible/hosts

runner[1:20].lab.mpt.local ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubunut/.ssh/id_rsa

```

### Privilege Escalation

When executing configuration management tasks and command, it often requires to escalate the user priviliege to ***root*** or ***admin*** via **sudo**. You can tell Ansible to run privilege tasks by specifying the ***become*** and ***become_user*** parameters. If ***become-user*** is omitted, ***root*** is assumed. Also Ansible expects the user executing the privilege tasks is setup to use passwordless (NOPASS) in the ***/etc/sudoers*** file or ***/etc/sudoers.d/*** directory.

In a command-line interface,
```
ansible ... --become --become-user=root ... -m <module> -a <arguments>
```

In a playbook,
```
# Tell Ansible to become the root user to execute all host commands.
- hosts: all
  become: yes
  become_user: root
```


### Lab Exercises

#### Exercise 1 - View the SSH configuration file

From Terminal window of the Ansible control machine, run the following commands:
```console
# View the SSH server configuration file
$ sudo cat /etc/ssh/sshd_config

# View the SSH client configuration file
$ sudo cat /etc/ssh/ssh_config

# Check the SSH server authentication method
$ sudo grep -i "PermitRootLogin\|PasswordAuthentication" /etc/ssh/sshd_config
```

Root and password login authentication should be disabled. 


#### Exercise 2 - Check the supported encrypton cipher

```console
# See SSH server supported encryption
$ sudo sshd -T | grep "\(ciphers\|macs\|kexalgorithms\)" | perl -pe 's/,/\n/g' | sort -u

# See SSH client supported encryption
# ssh -Q cipher | cipher-auth | mac | kex | key
$ ssh -Q cipher
$ ssh -Q mac
$ ssh -Q -kex

# See supported encryption of the remote host
$ ssh -vv runner<n>.lab.mpt.local
```

#### Exercise 3 - Generate SSH key pair
```console 
$ mkdir -p ~/.ssh
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 2048 -C "$USER" -N "" -f id_rsa
$ ls -ltr 
```

#### Exercise 4 - Copy the public to the remote host
```console
# Ping the if the remote host is reachable by Ansible
ansible runner1.lab.mpt.local -m ping -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu

# Use Ansible authorized_key module to copy the SSH public key to the remote host
$ ansible dev01 --become -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m authorized_key -a "user=$USER state=present key={{lookup('file', '~/.ssh/id_rsa.pub')}} manage_dir=yes"

# Now test the ssh remote connection
$ ssh $USER@runner<n>.lab.mpt.local

```

#### Exercise 5 - Configure Linux Sudoers File

The sudoers file is a file use to allocate system rights to system users. This allows the administrator to control who does what. 

```console
# Check the sudoers files
$ sudo ls -ltr /etc/sudoers*
$ sudo cat /etc/sudoers
$ sudo cat /etc/sudoers.d/<file>

# Check user groups
$ groups
$ sudo groups $USER
```

To enable passwordless sudo command, you create a file in /etc/sudoers.d/$USERS.

```
$USERS ALL=(ALL) NOPASSWD:ALL
```
For example,

```
$ sudo ls -ltr /etc/sudoers.d/ubuntu
-r--r----- 1 root root  31 Feb 21  2019 ubuntu

$ sudo cat /etc/sudoers.d/ubuntu
ubuntu ALL=(ALL) NOPASSWD:ALL

```

**Note**, make you have the correct syntax and file permission. Otherwise you may disable all sudoers and requires recovering or reinstalling the operating system.


---
### End of LAB2
