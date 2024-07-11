# How to deploy 
## Main server
Sure, here's the English documentation for setting up a virtual machine with Ansible, configuring domain and subdomains, setting up an SSH key on the target server, and running the Ansible playbook:

---

# VM Setup with Ansible

This guide will help you set up a virtual machine (VM) with Ansible, configure domain and subdomains, and deploy necessary configurations using an Ansible playbook.

## Prerequisites

1. **VM with Ansible Installed**
2. **Domain and Subdomains**
3. **SSH Key**

## Configuration

### 1. Setting Up the Domain and Subdomains

In your Ansible project directory, create or edit the `vault.yml` file to include your domain and subdomains:

```yaml
jenkins_domain: jenkins.example.com
traefik_domain: traefik.example.com
checkmk_domain: checkmk.example.com
authentik_domain: authentik.example.com
```

### 2. Adding SSH Key to Target Server

Copy your SSH public key to the target server:

```bash
ssh-copy-id -i ~/.ssh/your_key_name.pub user@target-server-ip
```

Replace `user` with your target server's username and `target-server-ip` with its IP address.

### 3. Running the Ansible Playbook

Run the playbook with the following command:

```bash
ansible-playbook backup-server.yml --ask-vault-pass
```

This command will prompt you for the vault password. You can find the password in your documentation at `deployment/ansible.md`.


## Production server
#### Manual configuration 
- **Gitlab plugin**: To install the GitLab plugin in Jenkins, navigate to the Jenkins dashboard and click on "Manage Jenkins". Then, select "Manage Plugins." In the "Plugin Manager" page, go to the "Available" tab and search for "GitLab". Jenkins will then download and install the plugin. 

- **Security settings**: To ensure secure communication and configuration in Jenkins, navigate to the Jenkins dashboard, click on "Manage Jenkins", and select "Configure Global Security". In the security settings, enable the proxy setting (CSRF Protection) to enhance security measures.

- **User settings**: To configure an easier password, navigate to the Jenkins dashboard. Click on your username, then go to the admin section. In the configuration settings, change the default password. Save the configuration, and you will need to log in again with the new password. 

- **Gitlab connection**: To set up a connection between Jenkins and GitLab, go to your GitLab account and create a personal access token with the necessary scopes for Jenkins integration. Return to the Jenkins dashboard and click on "Manage Jenkins," then select "Configure System." In the "GitLab" section, add the GitLab server details and the personal access token created in GitLab.

- **Create a CI/CD Pipeline**: To configure a CI/CD pipeline for your project, navigate to your Jenkins dashboard, click on "New Item," and select "Pipeline." Configure the pipeline by specifying the GitLab repository and defining the stages and steps for your CI/CD process.

  - Necessary steps in web platform:  
  1. Go to dashboard, select the correct pipeline that you just made and click configuration.   
  2. Go to the section pipeline and choose "pipeline script from SCM". 
  3. Write the correct repository URL (choose the correct branch) and add new credentials. 
  4. Click safe. 

  - Necessary steps in destination container: 
    1. `docker exec -it [containerID] /bin/bash`
    2. `ssh-keygen`
  
  - Necessary steps in Jenkins container:
    1. `ssh-keygen`
    2. `cat id_rsa.pub` and copy the key in the destination container in 'authorized_keys' 
    3. You have do do a manual ssh connection: `ssh root@[ip]`

  5. Go to dashboard, select "Manage Jenkins". Go to credentials and ad a new credential (secret file). Upload the `vault.yml` (ID = VaultFile) and upload the `.env` file (ID = EnvFile). 
  6. In the jenkinsfile: 
    - Go to dashboard, select "Manage Jenkins". Go to credentials and copy the correct ID. 

    `git credentialsId: '[correct ID]', branch: 'dev', url: 'https://gitlab.apstudent.be/nox/znz-infra.git'`