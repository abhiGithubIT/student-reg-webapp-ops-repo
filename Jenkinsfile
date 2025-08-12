pipeline {
    agent {label 'ansible'}

    options {
      buildDiscarder logRotator(numToKeepStr: '5')
      timeout(time: 10, unit: 'MINUTES')
      disableConcurrentBuilds()
    }

    stages {
        // Using Static Inventory file
        stage('Ping Tomcat Server') {
            steps {
                withCredentials([file(credentialsId: 'AWSEC2PEM', variable: 'aws_pem_file')]) {
                   sh "ansible _tomcat -i ansible-scripts/hosts -m ping -e ansible_ssh_private_key_file=${aws_pem_file} --ssh-common-args='-o StrictHostKeyChecking=no'"
                 }

            }
        }
        stage("Install Tomcat" ) {
            steps {
                withCredentials([file(credentialsId: 'AWSEC2PEM', variable: 'aws_pem_file')]) {
                   sh "ansible-playbook -i ansible-scripts/hosts ansible-scripts/installTomcat.yaml -e ansible_ssh_private_key_file=${aws_pem_file} --ssh-common-args='-o StrictHostKeyChecking=no'"
                 }
            }
        }
    }
}
