## LAB4 - Playbooks

### Goal
In this lab, you will learn the Ansible playbook structure by writing a playbook to install Elasticsearch ELK 

#### Playbook Format

Playbooks are YAML files containing a series of directives to provision and bring a system to a desired state. When writing in YAML, you need to be extra careful to maintain the correct indentation and use ***space*** instead of  ***tab***

Below is a simple playbook to install unzip and tree command tools.

```
---
- hosts: all
  become: true
  tasks:
  - name: install unzip 
    apt: 
      update_cache: yes
      name: {{item}}
    loop:
      - unzip
      - tree
```


### Lab Exercises

#### Exercise 1 - Create a playbook boilerplate

In the playbooks directory of the ansible project, `~/bootcamp/ansible/playbooks, create the playbook file, ***lab4.yml***, and add the following lines.

```
---
# The playbook to install and configure Elasticsearch ELK
# http:<hostname>5601

# The play below are the tasks to install Elasticsearch ELK
- hosts: all
  become: yes
  gather_facts: true
```

#### Exercise 2 - Working with variables

Variables give you more flexibility in writing playbooks and roles. They can be used to loop through a set of given values, access various information like the hostname of a system and replace certain strings in templates by with specific values. They are referenced via curly brackets **{{ }}**.

Append the variable section to the playbook (lab4.yml). These variables define the version and attributes to install Elasticsearch.
```
  vars:
    elastic_owner: elastic
    elastic_group: elastic
    elastic_version: 7.3.2
    elastic_pkg_folder: 7.x
    elastic_home: /apps/elastic
```

#### Exercise 3 - Tasks




See the complete playbook, [deploy-assets-manager.yml](../playbooks/deploy-assets-manager.yml)

