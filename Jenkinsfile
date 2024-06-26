pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install tools') {
            agent { label 'ansible' }
            steps {
                echo 'Installing the required tools'
                ansiblePlaybook(
                    playbook: '01.installations.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Build') {
            agent { label 'ansible' }
            steps {
                echo 'Building the webapp on the build server'
                ansiblePlaybook(
                    playbook: '02.build.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Test') {
            agent { label 'ansible' }
            steps {
                echo 'Testing the webapp on the build server'
                ansiblePlaybook(
                    playbook: '03.test.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Deploy Artifacts') {
            agent { label 'ansible' }
            steps {
                echo 'Copying the war file from the build server to tomcat servers'
                ansiblePlaybook(
                    playbook: '04.warfile.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

        stage('Restart Tomcat') {
            agent { label 'ansible' }
            steps {
                echo 'Starting tomcat'
                ansiblePlaybook(
                    playbook: '05.restart.yml',
                    inventory: 'hosts.ini'
                )
            }
        }

    }

    post {
        success {
            echo 'Pipeline succeeded! Send success notification.'
            mail to: 'okorjichika.co@gmail.com',
                 subject: "Success: ${currentBuild.fullDisplayName}",
                 body: "Build, test, and deployment done. Congratulations BabyG!"
        }
        failure {
            echo 'Pipeline failed! Send failure notification..'
            mail to: 'okorjichika.co@gmail.com',
                 subject: "Failed: ${currentBuild.fullDisplayName}",
                 body: "you tried abeg! e no easy."
        }
    }
}