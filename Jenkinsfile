pipeline {
    agent any

    tools {
        git 'Default'
    }

   environment {
    SONARQUBE_SERVER = 'Sonar local' 
    SONAR_SCANNER = 'SonarScanner' 
  }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Jachacon12/threepoints_devops_webserver.git'
            }
        }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${env.SONARQUBE_SERVER}") {
          script {
            def scannerHome = tool "${env.SONAR_SCANNER}"
            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=devops-sonar -Dsonar.sources=."
          }
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
