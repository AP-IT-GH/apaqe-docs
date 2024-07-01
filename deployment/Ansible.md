# Ansible

Ansible will be used to automate server configuration so that all the software has the necessary dependencies and environmental settings to be able to work.

## FLWSB-backend

All these steps are to deploy the infrastructure incase it would be necessary to redeploy again.

The main ansible playbook will do the following:

* grab all your variables from the encrypted "vault.yml"
* Install ufw - firewall
* update the system
* clear all services that could be running on port 80 by default
* install dependencies necessary for runnning our infrastructure
* install docker for either debian and rocky systems
* copy files from ansible host to target system
* determine the server type
* generate certificates and auto renew them periodicaly **if** the previous task aknowledged the server type as hetzner
  * locally this will not generate the certificates
* create the traefik network
* run docker compose up

## Vault

Ansible Vault is a feature of ansible that allows you to keep sensitive data such as passwords or keys in encrypted files, rather than as plaintext in playbooks or roles. These vault files can then be distributed or placed in source control.

To enable this feature, a command line tool - [ansible-vault](https://docs.ansible.com/ansible/2.9/cli/ansible-vault.html#ansible-vault) - is used to edit files, and a command line flag ([`<span class="pre">--ask-vault-pass</span>`](https://docs.ansible.com/ansible/2.9/cli/ansible-playbook.html#cmdoption-ansible-playbook-ask-vault-pass), [`<span class="pre">--vault-password-file</span>`](https://docs.ansible.com/ansible/2.9/cli/ansible-playbook.html#cmdoption-ansible-playbook-vault-password-file) or [`<span class="pre">--vault-id</span>`](https://docs.ansible.com/ansible/2.9/cli/ansible-playbook.html#cmdoption-ansible-playbook-vault-id)) is used. Alternately, you may specify the location of a password file or command Ansible to always prompt for the password in your ansible.cfg file. These options require no command line flag usage.

We will be using ansible vault to encrypt our variables while we store them on github. the current password for this is **P@ssw0rd**

For more information you can find it [here](https://docs.ansible.com/ansible/2.9/user_guide/vault.html) .
