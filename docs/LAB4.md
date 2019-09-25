## LAB4 - Roles
---

The concept of an Ansible role is a group of variables, tasks, files, and handlers that are stored in a standardized file structure.

You use ansible-galaxy init <ROLE_NAME> to create a new role skeleton in the ***roles*** directory. 

### Exercise 1 - Create an Apache role

In Lab2 exercise 5, we created a playbook to install Apache.


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
      notiffy: restart apache
    
  handlers:
    - name: restart apache
      service: name=apache2 state=restarted  
    
```

Let's now convert the playbook to a role. From your control machine,

```console
$ cd ~/bootcamp/ansible/playbooks/roles
$ ansible-galaxy init myapache
$ tree myapache
```

The *tree myapache* should show you the skeleton of the new role.

At the myapache role directory, open the file, *tasks/main.yml*

```console
$ cd ~/bootcamp/ansible/playbooks/roles/myapache
$ vi tasks/main.yml

```

Add the following lines to the file,

```
---
# tasks file for myapache

- name: Update apt-cache 
  apt: update_cache=yes

- name: Install apache2
  apt: name=apache2 state=present

- name: Enable Mod_Rewrite
  command: a2enmod rewrite
  notify: restart apache

```

Now open the the file, *handlers/main.yml*, to add the restart handler.

```
# handlers file for myapache

- name: restart apache
  service: name=apache2 state=restarted  

```

### Exercise 2 - Calling the role in a playbook

In the *playbooks* directory, create a playbook file, *deploy-myapache.yml*. Add the following lines

```
---
# playbook to deploy myapache

- hosts: all
  become: true

  roles:
    - { role: myapache }

```

Now run the playbook to deploy myapache on the managed node.

```console
$ ansible-playbook -i "runner<n>.lab.mpt.local," -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-myapache.yml
```

To confirm apache is installed, open the following URL.

http://runner\<n\>.missionpeaktechnologies.com

---
### LAB4 - End