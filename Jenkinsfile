pipeline {
    agent any

    tools {
        git 'Default'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Jachacon12/threepoints_devops_webserver.git'
            }
        }

        stage('Pruebas de SAST') {
            steps {
                echo 'Ejecución de pruebas de SAST'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t devops_ws .'
            }
        }
    }
}
