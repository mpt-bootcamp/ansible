## LAB5 - Configuration and Deployment

In this last lab, we will run the playbooks to configure a popular LAMP stack, deploy a Java web application. If time permitted, we will also configure a system to run the Elasticsearch and Kibana.

At the control machine, go to playbooks directory of the ansible project.


### Exercise 1 - Setup the inventory file for the lab environment

```console
$ cd ~/bootcamp/ansible/playbooks
$ mkdir -p inventories/lab
$ vi inventories/lab/hosts
```

Add or paste the following lines.

```
[web]
runner<n>.lab.mpt.local
```

### Exercise 2 - Configuring a LAMP stack

```console
$ cd ~/bootcamp/ansible/playbooks
$ ansible-playbook -i inventories/lab -u ubuntu $ --private-key=~/.ssh/id_rsa_ubuntu -v $ deploy-lamp.yml
```

### Exercise 3 - Deploy a Java web application

```console
$ cd ~/bootcamp/ansible/playbooks
$ ansible-playbook -i inventories/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -v deploy-assets-manager.yml
```

### Exercise 4 (Optional) - Configure Elasticsearch and Kibana

```console
$ cd ~/bootcamp/ansible/playbooks
$ ansible-playbook -i inventories/lab -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu -v deploy-elastic-elk.yml
```

To checkout the configuration and application, open the following URLs:

```
http:runner<n>.missionpeaktechnologies.com
http:runner<n>.missionpeaktechnologies.com:9000
http:runner<n>.missionpeaktechnologies.com:5601
```

---
### End of LAB5
