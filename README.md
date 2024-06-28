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
* generate certificates and auto renew them periodicaly IF the previous task aknowledged the server type as hetzner
* create the traefik network
* run docker compose up

## Monitoring

There will be 2 playbooks, 1 is the checkmk-server which will be to set up the central connection point of all devices that will be getting monitored. this has to be a very secure server.

the other playbook is for all devices that need to be monitored, this is called an agent. installing this should automatically add the device to the main checkmk server.

Please note that setting the variables correct, according to your infrastructure, is always necessary.
