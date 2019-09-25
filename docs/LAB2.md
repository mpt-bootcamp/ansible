## LAB2 - Writing a Playbook
---
A playbook is a YAML file containing a series of tasks to automate the configuration of a server.

A task defines a single step that should be executed by Ansible. It typically involves the usage of a module or the execution of a command.

### Task Format

```
- name: A brief description of task
  apt: name=unzip state=latest
```

The **name** part shows up in the output during the execution. The **apt** part, in this example, is a built-in Ansible module that abstracts the management of packages on Debian-based distributions to ensure the latest version of *unzip* tool is installed. 

### Playbook Format

Here is a simple playbook to install unzip. The **become** part tells Ansible to escalate the privilege as the *root* user. The **host** part let Ansible know to run on all managed nodes define in the inventory file (*/etc/ansible/hosts*).

```
---
- hosts: all
  become: true

  tasks:
    - name: Update apt-cache 
      apt: update_cache=yes

    - name: Install unzip
      apt: name=unzip state=latest
```

#### Exercisie 1 - Create and execute a simple playbook.

Create a *lab2a.yml* file and copy contents of the simple playbook above to the file.

```console
$ cd ~/bootcamp/ansible/playbooks
$ vi lab2a.yml
```

Next, execute the playbook.

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab2a.yml
```

#### Excercise 2 - Working with Variables

You can variables in a playbook with the **vars** keyword. Let change the simple playbook a the *package* variable. To access the value of a variable, enclose the variable name inside the double bracket **{{** *variable* **}}**.

Create a new playbook file, *lab2b.yml* and paste the contents below to the file.

```
---
- hosts: all
  become: true

  vars:
    package: unzip
    
  tasks:
    - name: Update apt-cache 
      apt: update_cache=yes

    - name: Install unzip
      apt: name={{ package }} state=latest

```

Run the playbook. You should see it runs but no change because it is already installed. It is the same as lab2a except the installing package represent as a variable instead a literal value. 

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab2b.yml
```

#### Exercise 3 - Using Loops

Loops are typically used to repeat a task using different input values. In this exercise, we will loop over a list of packages to install.

Create a playbook file, lab2c.yml, and copy the following contents to it. Here we will install 3 packages - unzip, tree, and ntp.

```
---
- hosts: all
  become: true

  tasks:
    - name: Update apt-cache 
      apt: update_cache=yes

    - name: Install unzip
      apt: name={{ item }} state=latest
      loop:
        - unzip
        - tree
        - ntp
```

Now, run the playbook see how it works.

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab2c.yml
```

You can also repreent the list of packages in a variable like below:

```
---
- hosts: all
  become: true

  vars:
    packages: ['unzip', 'npt', 'tree']

  tasks:
    - name: Update apt-cache 
      apt: update_cache=yes

    - name: Install unzip
      apt: name={{ item }} state=latest
      loop: "{{ packages }}"
```

**Note** that the *packages* variable is enclosed in double qoute. Otherwise, you will get an error when running the playbook. Ansible requires variable to enclose in quote if there is no other character(s) proceeding the variable.

#### Exercise 4 - Using Conditionals and Debugging

Conditionals can be used to decide whether or not a task should be executed, based on a variable or an output (registered variable) from a command.

In this exercise, we will create the playbook to check if PHP is installed and use the **debug** module to print the message and show the *register variable* of an output.

Create and copy the contents below to a new playbook file, *lab2d.yml* 

```
---
- hosts: all
  become: true

  tasks:
    - name: Check if PHP is installed
      register: php_installed
      command: php -v
      ignore_errors: true

    - name: This task is only executed if PHP is installed
      debug: var=php_install
      when: php_installed | success

    - name: This task is only executed if PHP is NOT installed
      debug: msg='PHP is NOT installed'
      when: php_installed | failed
```

To run the playbook,

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab2d.yml
```


#### Exercise 5 - Defining and Triggering Handler

A handlers is used to trigger a state change in a service, such as a restart or a stop. It is only executed when a notify directive is called in a task. 

In this exercise, we will create a playbook to install Apache2 and see how to use a restart handler after enabling the Mod_Rewrite.

Create a playbook file, *lab2e.yml*, with the following lines.

```
---
- hosts: all
  become: true

  tasks:
    - name: Update apt-cache 
      apt: update_cache=yes

    - name: Install apache2
      apt: name=apache2 state=present

    - name: Enable Mod_Rewrite
      command: a2enmod rewrite
      notify: restart apache
    
  handlers:
    - name: restart apache
      service: name=apache2 state=restarted  
    
```

To test the playbook,

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab2e.yml
```




---
## LAB2 - End