# ansible_install_packages 

* Ansible playbook to install services on gcp/aws compute instances (services like tomcat, jboss, kafka etc.). Currently, only tomcat service is available for installation
* To install ansible, run `python3 -m pip install ansible`
* Install Ansible plugin from Plugins manager in Jenkins. To configure Ansible plugin after installation go to Manage Jenkins --> Tools --> Ansible Installations and provide the path to the directory that contains ansible, ansible-playbook and ansible-inventory binaries. Usually, this location would be `/usr/local/bin`
* Jenkins credentials should contain: ssh private key (jenkins credential type: SSH Username with private key, credential id: ```remote_target_ssh_priv_key```) to connect to the target host(s)
* If installing the service on gcp compute instance(s), Jenkins credentials should contain (jenkins credential type: secret file): gcp service account key (jenkins credential id: ```gcp_service_account_key```) 
* If installing the service on aws ec2 instance(s), Jenkins credentials should contain (jenkins credentials type: secret text): aws access key id (jenkins credential id: ```aws_access_key_id```) and aws secret access key (jenkins credential id: ```aws_access_key```)
* Target host(s) should have python3 installed (run `sudo yum install python3` on the Jenkins server) and rpm based operating system that uses yum based package manager

### Prerequsite: For gcp instance: should have a label with key - 'technology', which represents the service that needs to be installed. For EC2 instance: should have a tag with key - 'technology'. Example: technology: tomcat

* rust (>=1.56.0) is required during Ansible installation on the Jenkins agent (Rust is only required for Ansible installtion. After installing rust, set rust compiler path with the command `. $HOME/.cargo/env` in the shell or `source  $HOME/.cargo/env` in bash. You can uninstall rust after Ansible installation). Make sure rust is installed for the user, the Jenkins runs as, before installing ansible on the jenkins agent. You can install rust (you can customize rust installation for minimal installation) on the Jenkins agent (Linux) with ```curl https://sh.rustup.rs -sSf | sh```

## Jenkins Configuration
 
### Jenkinsfile based CICD

* Jenkins input: ```Service``` - service that needs to be installed (Currently supported services: tomcat), 	(```AWS_EC2``` == true OR ```GCP_PROJECT```: gcp project id)
* Additional required Jenkins plugins: AnsiColor


## Tomcat specific

* [WIP][Fix]Tomcat doesn't start automatically, need to start manually by logging into the machine after the installation (Command: sudo tomcatup)
###### Tested with Jenkins 2.409, Python 3.9.16, ansible 8.0.0 (ansible-core 2.15.0) and Ansible plugin version: 217.v1696cee03265
