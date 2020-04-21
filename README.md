# ansible_install_packages

* Ansible playbook to install services on gcp/aws compute instances (services like tomcat, jboss, kafka etc.). 
* Jenkins credentials should contain: ssh private key (jenkins credential type: secret file, credential id: ```ssh_key```) to connect to the compute instance. Following users must be configured on the remote instances - AWS EC2: ```ec2-user``` GCP: ```jenkins```
* If installing the service on gcp compute instance, Jenkins credentials should contain (jenkins credential type: secret file): gcp service account key (jenkins credential id: ```gcp_service_account_key```) 
* If installing the service on aws ec2 instance, Jenkins credentials should contain (jenkins credentials type: secret text): aws access key id (jenkins credential id: ```aws_access_key_id```) and aws secret access key (jenkins credential id: ```aws_access_key```)
* Target host should have python3 installed and rpm based operating system that uses yum based package manager

### Prerequsite: For gcp instance: should have a label with key - 'technology', which represents the service that needs to be installed. For EC2 instance: should have a tag with key - 'technology'. Example: technology: tomcat

## Jenkins Configuration
 
### Jenkinsfile based CICD

* Jenkins input: ```Service``` - service that needs to be installed (Currently supported services: tomcat), 	(```AWS_EC2``` == true OR ```GCP_PROJECT```: "gcp project id")

* Additional required Jenkins plugins: AnsiColor

## Tomcat specific

* [WIP][Fix]Tomcat doesn't start automatically, need to start manually by logging into the machine after the installation (Command: sudo tomcatup)
