## LAB1 - Getting Started
---

Ansible is widely used to automate the configuration of a wide range of systems and devices such as virtual machines, databases, storage devices, networks, firewalls, etc. Generally, you can use Ansible to automate two types of tasks:

1. Configuration management - Change the configuration of an application, OS, or device; start and stop services; install or update applications; implement a security policy.
2. Application deployment- Make DevOps easier by automating the build and deployment of your applications.

### Exercise 1 - Connecting to the Lab Environment

For the lab exercises, we will be connecting to an Ansible control machine (EC2 intance) in AWS. Each student is assigned with a student number (1 to 20). You will need to replace the **\<n\>** with the assigned number in the lab instructions.


To connect to the control machine, 

1) Open the URL with a browser (Chrome or Firefox).

```
http://console<n>.missionpeaktechnologies.com:8000/
```
2) Enter student\<n\>/student\<n\> for the username and passowrd. For example, student1/student1
3) After login, click the "Terminal" icon to open a web terminal session

![Custom Apache Home Page](images/console-home.png)

4) Enter the following commands to verified Ansible and required packages are installed

```console
$ cd ~/
$ pwd
$ whoam
$ echo $USER
$ which python
$ python --version
$ which ansible
$ ansible --version

````

5) Copy the Ansible sudo user (ubuntu) SSH key

```console
$ mkdir -p ~/.ssh
$ sudo cp /home/ubuntu/.ssh/id_rsa ~/.ssh/id_rsa_ubuntu
$ sudo chown $USER: ~/.ssh/id_rsa_ubuntu
$ ls -ltr ~/.ssh

```

You should see something like below.

```
student1@console1:~$ ls -la ~/.ssh
drwxrwxr-x 2 student1 student1 4096 Sep 16 03:01 .
drwxr-xr-x 5 student1 student1 4096 Sep 16 03:57 ..
-rw------- 1 student1 student1 1675 Sep 16 03:01 id_rsa_ubuntu
```

With the Jupyter web terminal, you can also the **Text File** and the **Markdown File** editors to create or view files. Click the four icons on the left of the navigation panel to open the File, Terminal, Help, and Laucher explorer or viewer.


### Exercise 2 - Using Ansible CLI

Now you are able to connect to the control machine via the Jupyter web terminal, let's learn how to use the Ansible command-line interface (CLI). The **ansible** CLI command is commonly used to execute some simple built-in modules.

1) Run the **ping** module to check a remote host is reachable.

```console
$ ansible all -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m ping

```

Here *-i "runner<n>/lab.mpt.local,"* is used to specify the dynamic host, host is not define in the inventory file. The *-u ubuntu --private-key=~/.ssh/id_rsa_ubuntu* tells Ansible to use the escalated privilege account *ubuntu* and use the private key for connection authentication.

2) Run the **setup** module to gather the system information (Ansible facts).


```console
$ ansible all -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m setup

```

3) To learn all the command option, type

```consoel
$ ansible --help
```


### Exercise 3 - Setting the Default Inventory File

The inventory file can list individual hosts or user-defined groups of hosts. The default location for the inventory file is ***/etc/ansible/hosts***.

1) Open or create the default inventory file to add a managed node and a database node in the *db* group

```console
$ sudo vi /etc/ansible/hosts
```

Add or append the following lines to the host file, */etc/ansible/hosts*.

```
runner<n>.lab.mpt.local

[db]
runner1.lab.mpt.local

```

Note to replace \<n\> with your student number. Here we also designate *runner20.lab.mpt.local* as a share database host under the *db* group. *lab.mpt.loca* is just a local acessible domain name.


2) With the managed node added to the inventory file, you can run the ansible command by providing the *host* or *group* name.

To ping a specific host in the inventory file

```console
$ ansible runner<n>.lab.mpt.local -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m ping
```

To ping all hosts in the inventory file,

```console
$ ansible all -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -m ping
```

Note the ***all*** after the **ansible** command. You should prompt to confirm to add to the known_hosts file.

### Exercise 4 - Clone the Ansible Project from GitHub

Let's download (clone) the Ansible project before we continue the rest of the labs.

From your Ansible control machine,

```console
$ mkdir -p ~/bootcamp
$ cd ~/bootcamp
$ git clone https://github.com/mpt-bootcamp/ansible.git
```

To brow the directory structure, you can run the Linux command tree.

```console
$ cd ~/bootcamp/ansible
$ tree
```

---
## LAB1 - End
