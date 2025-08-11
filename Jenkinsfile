pipeline {
    agent { label 'ansible' }

    stages {
        stage('Git Clone') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/abhiGithubIT/student-reg-webapp-ops-repo.git',
                        credentialsId: 'GithubCred'
                    ]],
                    gitTool: 'Default' // Forces use of configured Git tool
                ])
            }
        }

        stage('Ping Tomcat Server') {
            steps {
                withCredentials([file(credentialsId: 'AWSEC2PEM', variable: 'aws_pem_file')]) {
                    sh """
                        ansible tomcatServers -i ansible-scripts/hosts -m ping \
                        -e ansible_ssh_private_key_file=${aws_pem_file} \
                        --ssh-common-args='-o StrictHostKeyChecking=no'
                    """
                }
            }
        }

        stage('Install Tomcat') {
            steps {
                withCredentials([file(credentialsId: 'AWSEC2PEM', variable: 'aws_pem_file')]) {
                    sh """
                        ansible-playbook -i ansible-scripts/hosts ansible-scripts/installTomcat.yaml \
                        -e ansible_ssh_private_key_file=${aws_pem_file}
                    """
                }
            }
        }
    }
}
