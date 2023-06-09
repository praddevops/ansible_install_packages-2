def GCP_APP_CREDENTIALS
def INVENTORY_FILE
def ANSIBLE_EXTRA_VARS

pipeline {
    agent any
    

    options {
        ansiColor('xterm')
        timestamps()
    }
    
    parameters {
       string(name: 'Service', defaultValue: '', description: 'Service that needs to be installed')
       booleanParam(name: 'AWS_EC2', defaultValue: false, description: 'Check to create Tomcat Service in AWS EC2. If checked GCP_PROJECT will be ignored')
       string(name: 'GCP_PROJECT', defaultValue: '', description: 'GCP Project ID')  
       string(name: 'ANSIBLE_REMOTEUSER', defaultValue: 'jenkins', description: 'remote username for Ansible to connect as, to the remote machine')
    }

    environment {
       GCP_SERV_ACC_KEY = credentials('gcp_service_account_key')
       AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
       AWS_ACCESS_KEY = credentials('aws_access_key')
       TECHNOLOGY = "${params.Service}"
       AWS_PROJECT = "${params.AWS_EC2}"
       GCP_PROJECT_ID = "${params.GCP_PROJECT}"
       SSH_USER = "${params.ANSIBLE_REMOTEUSER}"
       ANSIBLE_HOST_KEY_CHECKING="false"
       ANSIBLE_RETRY_FILES_ENABLED="false"
       ANSIBLE_LOG_PATH="out.log"
    }
// 'source' is not supported in some debian based jenkins. so using '. ansible_env/bin/activate' instead of 'source ansible_env/bin/activate'
    stages {
        stage('Install Ansible') {
            steps {
              sh"""
              set +x
              . $HOME/.cargo/env
              mkdir -p ansible_env
              python3 -m venv ansible_env --system-site-packages
              . ansible_env/bin/activate
              python3 -m pip install --no-cache-dir --upgrade pip
              python3 -m pip install --no-cache-dir -r ansible-requirements.txt
              python3 -m pip list
              """
            }
        }
        stage('Configure Ansible') {
            steps {
             script{
                if ("${AWS_PROJECT}" == "true" ) {
                    print ("AWS_EC2 is selected")
                    INVENTORY_FILE="inventory/devops-project.aws_ec2.yml"
                    ANSIBLE_EXTRA_VARS="technology_name=${TECHNOLOGY} remote_user=${SSH_USER}"
                    sh """
                    sed -i "s,aws_accss_key_id, ${AWS_ACCESS_KEY_ID}," "${INVENTORY_FILE}"
                    sed -i "s,aws_key, ${AWS_ACCESS_KEY}," "${INVENTORY_FILE}"
                    """
                } else if ("${GCP_PROJECT_ID}" != null && "${GCP_PROJECT_ID}" != ''){ 
                    INVENTORY_FILE="inventory/devops-project.gcp.yml"
                    GCP_APP_CREDENTIALS = "gcp_key.json"
                    ANSIBLE_EXTRA_VARS="technology_name=${TECHNOLOGY} remote_user=${SSH_USER}"
                    sh """
                    cat "${GCP_SERV_ACC_KEY}" > "${GCP_APP_CREDENTIALS}"
                    sed -i "s,service_account_file:.*,service_account_file: ${GCP_APP_CREDENTIALS}," "${INVENTORY_FILE}"
                    sed -i 's/gcp_project_id/${GCP_PROJECT_ID}/' ${INVENTORY_FILE}
                    wget -q https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-288.0.0-linux-x86_64.tar.gz
                    tar -xzf google-cloud-sdk-288.0.0-linux-x86_64.tar.gz
                    ./google-cloud-sdk/bin/gcloud auth activate-service-account --key-file="${GCP_APP_CREDENTIALS}"
                    """ 
                }
              } 
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                withCredentials([string(credentialsId: 'ssh_key', variable: 'ssh_key_pem')]){
              sh """
                 set +x           
                 sed -i 's/technology_label_value/${TECHNOLOGY}/' ${INVENTORY_FILE}
                 . ansible_env/bin/activate
                 echo "${ssh_key_pem}" >> keyfile
                 chmod 400 keyfile
                 ansible-playbook --private-key keyfile -u ${SSH_USER} -i ${INVENTORY_FILE} ansible_playbook.yml -e "${ANSIBLE_EXTRA_VARS}"
                 """
                }
             }
        }
    }
    post { 
        always {
          cleanWs()
        }
    }
}
