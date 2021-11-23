# my-controller
My Ansible Automation Platform controller

## Description

Demo of utilizing the redhat_cop.tower_configuration collection (https://github.com/redhat-cop/tower_configuration) to configure Ansible Automation Platform (Tower/Controller).

The redhat_cop.tower_configuration collection turns post config of Tower/Controller into editing some yaml variable files and running a playbook.
See the variable files which defines which resources to get created in https://github.com/mglantz/my-controller/tree/main/controller

## Prereq
1. Install Ansible Automation Platform Tower or Controller
2. Install ansible-tower-cli
```
dnf config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
dnf install ansible-tower-cli
```
3. Setup authentication for the awx cli tool.
Create ~/.tower_cli.cfg as follows:
```
[general]
verify_ssl = false
oauth_token = alongerstringgoeshere
```
4. Install collections used
```
ansible-galaxy collection install ansible.tower
ansible-galaxy collection install awx.awx
ansible-galaxy collection install redhat_cop.tower_configuration
```

## Demo
```
git clone https://github.com/redhat-cop/tower_configuration
cd tower_configuration/examples
git clone https://github.com/my-controller
cp my-controller/aap-automate.yml .
# Adjust authentication
vi my-controller/controller/controller_auth.yml
ansible-playbook ./aap-automate.yml
```

### Example
```
$ ansible-playbook ./aap-automate.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Playbook to configure ansible Controller post installation] ****************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Include vars from configs directory] ***************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [redhat_cop.controller_configuration.settings : Update Ansible Controller Settings from dictionary or list of dictionaries] *************************************************************************************************************

TASK [redhat_cop.controller_configuration.organizations : Add organizations] *****************************************************************************************************************************************************************
ok: [localhost] => (item={'name': 'Default'})
[WARNING]: You are using the awx version of this collection but connecting to Red Hat Ansible Tower

TASK [redhat_cop.controller_configuration.labels : Add a label to Controller] ****************************************************************************************************************************************************************
ok: [localhost] => (item={'name': 'Dev', 'organization': 'Default'})
ok: [localhost] => (item={'name': 'Prod', 'organization': 'Default'})

TASK [redhat_cop.controller_configuration.users : Add controller user] ***********************************************************************************************************************************************************************
changed: [localhost] => (item={'user': 'automation_user', 'is_superuser': False, 'password': 'redhat123'})
[WARNING]: The field password of user 2 has encrypted data and may inaccurately report task is changed.

TASK [redhat_cop.controller_configuration.teams : Create Ansible Controller Team] ************************************************************************************************************************************************************
ok: [localhost] => (item={'name': 'automation-users', 'organization': 'Default'})

TASK [redhat_cop.controller_configuration.credentials : Add Credentials] *********************************************************************************************************************************************************************
changed: [localhost] => (item=None)
changed: [localhost]

TASK [redhat_cop.controller_configuration.projects : Add Projects] ***************************************************************************************************************************************************************************
changed: [localhost] => (item={'name': 'Linux team playbooks', 'scm_type': 'git', 'scm_url': 'https://github.com/mglantz/ansible-playbooks', 'scm_branch': 'master', 'scm_clean': True, 'description': "The Linux team's playbooks", 'organization': 'Default', 'wait': True, 'update': True})

TASK [redhat_cop.controller_configuration.inventories : Create inventory] ********************************************************************************************************************************************************************
changed: [localhost] => (item={'name': 'localhost', 'description': 'inventory for localhost', 'organization': 'Default'})
changed: [localhost] => (item={'name': 'Linux servers', 'description': 'Linux servers', 'organization': 'Default'})

TASK [redhat_cop.controller_configuration.hosts : Add Controller host] ***********************************************************************************************************************************************************************
changed: [localhost] => (item={'name': 'localhost', 'inventory': 'localhost', 'variables': {'some_var': 'some_val', 'ansible_connection': 'local'}})
changed: [localhost] => (item={'name': 'rhel8c.sudo.net', 'inventory': 'Linux servers', 'variables': {'some_var': 'some_val'}})

TASK [redhat_cop.controller_configuration.groups : Add controller group] *********************************************************************************************************************************************************************
changed: [localhost] => (item={'name': 'rhel8', 'inventory': 'Linux servers', 'variables': {'some_var': 'some_val'}, 'hosts': ['rhel8c.sudo.net']})

TASK [redhat_cop.controller_configuration.job_templates : Add Controller Job Templates] ******************************************************************************************************************************************************
changed: [localhost] => (item={'name': 'Check if rhel8c is alive', 'description': 'Login to rhel8c and see if a command returns output', 'job_type': 'run', 'inventory': 'Linux servers', 'labels': ['Prod'], 'credentials': 'Linux admin user', 'project': 'Linux team playbooks', 'playbook': 'ping.yml', 'verbosity': 0})

PLAY RECAP ***********************************************************************************************************************************************************************************************************************************
localhost                  : ok=12   changed=7    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   

$
```
