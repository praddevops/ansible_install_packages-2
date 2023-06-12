# ansible_install_packages

* Ansible playbook to install services on gcp/aws compute instances (services like tomcat, jboss, kafka etc.). 
* Jenkins credentials should contain: ssh private key (jenkins credential type: SSH Username with private key, credential id: ```ssh_key```) to connect to the compute instance
* If installing the service on gcp compute instance, Jenkins credentials should contain (jenkins credential type: secret file): gcp service account key (jenkins credential id: ```gcp_service_account_key```) 
* If installing the service on aws ec2 instance, Jenkins credentials should contain (jenkins credentials type: secret text): aws access key id (jenkins credential id: ```aws_access_key_id```) and aws secret access key (jenkins credential id: ```aws_access_key```)
* Target host should have python3 installed (run `sudo yum install python3` on the Jenkins server) and rpm based operating system that uses yum based package manager

### Prerequsite: For gcp instance: should have a label with key - 'technology', which represents the service that needs to be installed. For EC2 instance: should have a tag with key - 'technology'. Example: technology: tomcat

* rust (>=1.56.0) is required for Ansible. Make sure rust is installed for the user the Jenkins runs as. You can install rust (you can customize rust installation to select minimal) on the Jenkins node (Linux) with ```curl https://sh.rustup.rs -sSf | sh```

## Jenkins Configuration
 
### Jenkinsfile based CICD

* Jenkins input: ```Service``` - service that needs to be installed (Currently supported services: tomcat), 	(```AWS_EC2``` == true OR ```GCP_PROJECT```: gcp project id), 	ANSIBLE_REMOTEUSER (remote username for Ansible to connect as, to the remote instance. Ex: For AWS EC2 instance with Amazon Linux 2 AMI default username is: ec2-user)

* Additional required Jenkins plugins: AnsiColor


## Tomcat specific

* [WIP][Fix]Tomcat doesn't start automatically, need to start manually by logging into the machine after the installation (Command: sudo tomcatup)
