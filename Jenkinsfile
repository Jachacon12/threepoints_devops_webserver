pipeline {
    agent any

    tools {
        git 'Default'
    }

    environment {
        SONAR_SCANNER_OPTS = "-Dsonar.projectKey=devops-sonar"
        SONAR_TOKEN = credentials('new-sonar-key')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Jachacon12/threepoints_devops_webserver.git'
            }
        }

stage('Análisis SonarQube') {
  steps {
    withSonarQubeEnv('Sonar local') {
      sh 'sonar-scanner -Dsonar.projectKey=devops-sonar -Dsonar.sources=.'
    }
    timeout(time: 1, unit: 'MINUTES') {
      waitForQualityGate()
    }
  }
}


        stage("Esperar calidad") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops_ws .'
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh 'docker stop devops_ws || true'
                sh 'docker run -d -p 8090:8090 --name devops devops_ws'
            }
        }
    }
}
