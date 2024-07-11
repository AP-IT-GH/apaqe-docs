# CI/CD Pipeline
## What is a CI/CD? 
CI/CD stands for Continuous Integration and Continuous Deployment. Continuous Integration involves regularly merging code changes into a shared repository and running automated tests to catch issues early. Continuous Deployment takes it further by automatically deploying tested code to production. 
## Why do we use CI/CD
We use CI/CD to automate and streamline the software development lifecycle. It helps in integrating code changes frequently, running automated tests, and deploying applications reliably and quickly to production. This approach enhances productivity, reduces manual errors, and ensures that new features and updates reach users faster and with higher quality.

In our case, we use CI/CD to deploy code to the testing environment manually triggered. This process automates the integration of code changes, executes tests, and ensures that applications are deployed efficiently and reliably for testing purposes.
## Why do we use Jenkins
### What is Jenkins

Jenkins is a powerful automation server that facilitates Continuous Integration (CI) and Continuous Delivery (CD) pipelines for software development projects. As an extensible platform, Jenkins serves as both a simple CI server and a comprehensive hub for managing continuous delivery workflows.

### Key Features

1. **Easy Installation**: Jenkins is built in Java and offers straightforward installation packages for various operating systems, including Windows, Linux, macOS, and Unix-like systems. It's designed to be up and running quickly with minimal setup.

2. **Web Interface**: Configuration and management of Jenkins are done through a user-friendly web interface. This interface supports on-the-fly error checking and includes built-in help, making it accessible for users to configure their pipelines efficiently.

3. **Plugin Ecosystem**: Jenkins boasts a vast ecosystem of plugins available through its Update Center. These plugins enable seamless integration with a wide range of tools and services used in the CI/CD pipeline, enhancing Jenkins' flexibility and functionality.

4. **Extensibility**: The core strength of Jenkins lies in its extensibility through plugins. This architecture allows users to customize and extend Jenkins to meet specific automation needs, offering nearly limitless possibilities for integration and automation tasks.

5. **Distributed Builds**: Jenkins can distribute build and test tasks across multiple machines, enabling parallel execution of jobs across different platforms. This capability accelerates the build and deployment processes, enhancing efficiency in large-scale development environments.


### Use of Jenkins 
We do the setup with Ansible so you need to install Ansible in the docker container of Jenkins. 

Dockerfile: 
```
FROM jenkins/jenkins:jdk17
USER root
RUN apt-get update \
    && apt-get install -y \
        ansible \
    && rm -rf /var/lib/apt/lists/*

USER jenkins
````


#### Setting up Jenkins with Docker 

1. **Prepare docker-compose.yml**: Before setting up Jenkins, ensure you have a docker-compose.yml file configured to define your Jenkins environment. We use this: 

```yaml
version: "3"
services:
  jenkins:
    image: jenkins/jenkins:jdk17
    volumes:
      - jenkins-data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - ./ssh:/home/jenkins/.ssh
    labels:
      - "traefik.http.routers.jenkins.rule=Host (`jenkins.apaqe.eu`)"
      - "traefik.http.routers.jenkins.tls=true"
      - "traefik.http.routers.jenkins.tls.certresolver=letsencrypt"
      - "traefik.http.services.jenkins.loadbalancer.server.port=8080"
    networks:
      - proxy
volumes:
  jenkins-data:
networks:
  proxy:
    external: true
    name: traefik 
```
After this: 
```bash
docker compose up
```

2. **Retrieve Admin Password**:
After launching Jenkins using Docker Compose, you need to retrieve the initial admin password to proceed with setup. 
You can do this in 3 ways: 
- Use this command: 
```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
- Use this  command:
```bash
docker exec -it [containername]/bin/bash
```
- Go to docker logs: 
```bash
docker logs [containername]
```
3. **Install suggested Plugins**:
Once Jenkins is accessible via https://jenkins.apaqe.eu, access the Jenkins web interface. Follow these steps to install recommended plugins:

- Navigate to https://jenkins.apaqe.eu in your web browser.
- Follow the on-screen instructions to set up Jenkins, including entering the initial admin password retrieved earlier.
- Select the option to install suggested plugins during the setup process.

After the installation: You don't need to create first admin user, skip and continue as admin. 
Then safe and finish the instance configuration. 


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

    The JenkinsFile: 
    ```pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                script {
                    // Git repository checkout
                    git credentialsId: 'b17db829-5978-4565-8b23-60f13b1e31e3', branch: 'features/backend-traefik', url: 'https://gitlab.apstudent.be/nox/znz-infra.git'
                    
                    sh 'if [ -d "znz-infra" ]; then rm -rf znz-infra; fi'
                    sh 'mkdir -p znz-infra'
                    sh 'mv -f Jenkinsfile README.md ansible gateway src znz-infra/'
                }
            }
        }
        stage('Create Ansible Hosts File') {
            steps {
                script {
                    def hostsContent = """
                        [FLWSB_hetzner]
                        188.34.197.214 ansible_user=root
                    """
                    writeFile file: 'znz-infra/ansible/hosts', text: hostsContent
                }
            }
        }
        stage('Create .env File') {
            steps {
                    withCredentials([file(credentialsId: 'EnvFile', variable: 'VARS_FILE')]) {
                    script {
                        def envContent = sh(script: "cat \$VARS_FILE", returnStdout: true).trim()
                        writeFile file: 'znz-infra/src/.env', text: "${envContent}"
                    }
                }
            }
        } 
        stage('Run Ansible') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'VaultFile', variable: 'Vault_FILE')]) {
                        dir('znz-infra/ansible') {
                            sh '''
                                ansible-playbook FLWSB-backend-ansible.yaml -e @${Vault_FILE}
                            '''
                        }
                    }
                }
            }
        }
    }
}
```




