pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('sonar-key-new')
  }

  stages {
    stage('Tool Setup') {
      steps {
        script {
          env.JAVA_HOME = tool name: 'jdk-17', type: 'hudson.model.JDK'
          env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
        }
      }
    }

    stage('Checkout') {
      steps {
        git 'https://github.com/Jachacon12/threepoints_devops_webserver.git'
      }
    }

    stage('Análisis SonarQube') {
      steps {
        withSonarQubeEnv('Sonar local') {
          script {
            def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            sh """
              ${scannerHome}/bin/sonar-scanner \
                -Dsonar.projectKey=devops-sonar \
                -Dsonar.sources=. \
                -Dsonar.token=$SONAR_TOKEN
            """
          }
        }
      }
    }

    stage('Esperar calidad') {
      steps {
        timeout(time: 3, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t devops-webserver .'
      }
    }

    stage('Deploy Docker Container') {
      steps {
        sh '''
          docker stop devops-webserver || true
          docker rm devops-webserver || true
          docker run -d --name devops-webserver -p 3000:3000 devops-webserver
        '''
      }
    }
  }
}
