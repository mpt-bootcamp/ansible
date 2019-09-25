## LAB3 - SSH Basic and Privilege Escalation
---

### SSH Basic

Ansible uses native OpenSSH for remote communication and SSH keys for authentication. Although password authentication is supported, it is not recommended for security best practice. When the control machine connects to the managed nodes, it first establishes an agreed encryption to protect the communication session. Then it authenticates the given user and verify whether access to the server should be granted. 

When using SSH keys for authenticatin, Ansible uses the given user and private key in the command-line interface (CLI). The public key should be located in **~/.ssh/authorized_keys** on remote systems. The private key usually stores in the **~/.ssh** directory of the user home. Also, the user given is a ***sudoer*** user, meaning it has the escalated privilege to execute administrative commands ( with the sudo prefix before the commands). For example,


```console
# Linux sudo command restart the Apache service
$ sudo systemctl restart apapche2
```

If you don't specify the user and priviate key to use, Ansible assumes the current user (**$USER**) and private key (**~/.ssh/id_rsa**).


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


### Exercise 1 - View the SSH configuration file

From Terminal window of the Ansible control machine, run the following commands:

```console
# View the SSH server configuration file
$ sudo cat /etc/ssh/sshd_config

# View the SSH client configuration file
$ sudo cat /etc/ssh/ssh_config

# Check the SSH server authentication method
$ sudo grep -i "PermitRootLogin\|PasswordAuthentication" /etc/ssh/sshd_config
```

The output should show the toot and password login authentication are disabled. 


### Exercise 2 - Check the supported encrypton cipher

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

### Exercise 3 - Generate a SSH key pair

```console 
$ mkdir -p ~/.ssh
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 2048 -C "$USER" -N "" -f id_rsa
$ ls -ltr 
```

### Exercise 4 - Copy the public key to the managed node using Ansible CLI

```console
# Ping the if the remote host is reachable by Ansible
$ ansible runner<n>.lab.mpt.local -m ping -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu

# Use Ansible authorized_key module to copy the SSH public key to the remote host
$ ansible runner<n>.lab.mpt.local --become -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m authorized_key -a "user=$USER state=present key={{lookup('file', '~/.ssh/id_rsa.pub')}} manage_dir=yes"

```

### Exercise 5 - Login to the managed node using SSH connection

Now test the SSH connection using the SSH key pair

```console
$ ssh $USER@runner<n>.lab.mpt.local
```

---
## LAB3 - End

