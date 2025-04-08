pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Jachacon12/threepoints_devops_webserver.git'
            }
        }

stage('SonarQube Analysis') {
  steps {
    withSonarQubeEnv('Sonar local') {
      withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
        sh '''
          sonar-scanner \
            -Dsonar.projectKey=devops-sonar \
            -Dsonar.sources=. \
            -Dsonar.token=$SONAR_TOKEN
        '''
      }
    }
  }
}


        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh 'docker run -d --rm -p 3000:3000 --name myapp myapp:latest'
            }
        }
    }
}
