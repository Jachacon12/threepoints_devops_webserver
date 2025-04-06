pipeline {
    agent any

    tools {
        git 'Default'
        sonarQubeScanner 'SonarScanner'
    }

    environment {
        SONAR_SCANNER_OPTS = "-Dsonar.projectKey=Sonar local"
        SONAR_AUTH_TOKEN = credentials('sonar-key')
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
          sh '''
            docker run --rm \
              -v $PWD:/usr/src \
              -w /usr/src \
              sonarsource/sonar-scanner-cli:5.0 \
              -Dsonar.projectKey=devops-sonar \
              -Dsonar.sources=src \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_AUTH_TOKEN
          '''
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
