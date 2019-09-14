Ansible
=========

This project contains the playbooks and roles used for system configurations and deployments.

Ansible is an automation tool commonly used for cloud provisioning, configuration management, application deployment. It is agentless and simple to install and use.

Ansible works by connecting to the managed nodes from a control machine, and pushing out small programs, called "modules" to them. Ansible then executes these modules over SSH by default, and removes them when finished. While passwords are supported, but SSH keys are the preferred way to use Ansible.

Ansible represents machines it manages using a file (INI or YAML formatted) to put all of machines, or put them in groups.

```
[consoles]
console[1:20].missionpeaktechnologies.com


[runners]
runner[1:20].lab.mpt.local
```


Usage
------------

Playbooks can be played from the command-line interface or from an orchestration or operations management platform like Jenkins, Rundeck, or Ansible UI (AWX). For example,

### Using CLI

```console

$ ansible-playbook -i runner1.lab.mpt.local, -u ubuntu --private-key=~/.ssh/id_rsa_ubuntu deploy-assets-manager.yml
```

### Calling in a Jenkins Pipeline
```console

stage('Deployment') {
    ...
    ansiblePlaybook credentialsId: "${SSH_KEY}", 
        inventory: "${WORKSPACE}/playbooks/inventories/${params.ENV}/hosts", 
        playbook: "${WORKSPACE}/playbooks/deploy-assets-manager.yml",
        extras: "-e app_version=${params.VERSION}"
    ...
}
```


------------


