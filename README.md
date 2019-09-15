Ansible
=========

This project contains the playbooks and roles used for system configurations and deployments.

Ansible is an automation tool commonly used for cloud provisioning, configuration management, application deployment. It is agentless and simple to install and use.


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


