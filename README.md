# my-controller
My Ansible Automation Platform controller

## Description

Demo of utilizing the redhat_cop.tower_configuration collection (https://github.com/redhat-cop/tower_configuration) to configure Ansible Automation Platform (Tower/Controller).

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
