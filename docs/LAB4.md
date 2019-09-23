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

#### Exercise 3 - Executing tasks with modules and debugging

The goal of each task is to execute a **module**, with very specific arguments. Variables can be used in arguments to modules. Tasks are executed in order, one at a time, against all machines matched by the host pattern, before moving on to the next task. **pre_tasks** is a task which Ansible executes before executing any tasks mentioned in the playbook. 

To print a message from Ansible playbook, as well as a value of a variable, you can use Ansible **debug** module.

Within a task, you can use looping (**loop**), conditional (**when**), and **register** output to a variable.

Open the *lab4.yml* and appending the following tasks.


```
  pre_tasks:
    - name: create the elastic user group
      user:
        name: "{{ elastic_group }}"
        state: present

    - name: create the elastic service user
      user:
        name: "{{ elastic_owner }}"
        group: "{{ elastic_group }}"
        state: present

  tasks:
  - name: "check if the {{elastic_home}} directory exists"
    stat:
      path: "{{ elastic_home }}"
    register: elastic_home_check

  - name: print debug information
    debug: 
      var: elastic_home_check

  - name: show conditional messages and facts (OS distribution)
    debug:
      msg: "{{ elastic_home }} does not existing. default location /var/lib/elasticsearch will be used ({{ ansible_distribution }})"
    when: elastic_home_check.stat.exists == false   

  - name: add elasticsearch public signing key
    apt_key:
      url: "https://artifacts.elastic.co/GPG-KEY-elasticsearch"
      state: present
  
  - name: add elasticsearch repo definition
    apt_repository:
      repo: "deb https://artifacts.elastic.co/packages/{{ elastic_pkg_folder }}/apt stable main"
      update_cache: yes
      state: present

  - name: install elasticsearch ELK components
    apt:
      name: "{{item.name}}={{item.version}}"
      state: present
    loop:
      - {name: 'elasticsearch', version: '{{elastic_version}}' }
      - {name: 'kibana', version: '{{elastic_version}}' }
```

#### Excercise 4 - Using Handler

Handler are task that are defined inside or outside a play and can be called by the **notify** a state change during the task execution.


Open the *lab4.yml* playbook file and append the following tasks to enable the Elasticsearch services to start, restart when the state change.

```
  - name: configure kibana server.host
    lineinfile:
      dest: /etc/kibana/kibana.yml
      regexp: '^server.host:'
      line: 'server.host: "0.0.0.0"'
      state: present
    notify:
      - restart kibana     

  - name: start earch of the component services 
    service: 
      name: "{{ item }}" 
      enabled: yes 
      state: started
    loop:
      - elasticsearch
      - kibana 

  handlers:
  - name: restart kibana
    service: name=kibana state=restarted

  - name: restart elasticsearch
    service: name=elasticsearch state=restarted 

```

#### Exercise 5 - Error handling

When Ansible detect a error return from a task (command or module), it fails fast, forcing an error to be dealt with unless you decide otherwise. You can add one of the following error handling condition.

```
ignore_errors: yes | no
failed_when: \<expression\>
```

Open the *lab4.yml* playbook and add the following error handling task.

```
  - name: Fail task when the command error output prints FAILED
    command: /usr/bin/example-command -x -y -z
    register: command_result
    failed_when: "'FAILED' in command_result.stderr"
```

See the complete playbook, [deploy-assets-manager.yml](../playbooks/deploy-assets-manager.yml)

Now we have the completed playbook, let's play it to install the ELK stack.

```console
$ ansible-playbook --limit runner<n>.lab.mpt.local -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu lab4.yml

```

---
### End of LAB4