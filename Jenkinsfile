@Library('threepoints-shared-lib@main') _

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
        stage('Probar librería compartida') {
          steps {
            script {
              holamundo()
            }
          }
        }
        stage('Análisis de Calidad') {
            parallel {
        stage('Pruebas de SAST') {
            steps {
                script {
                    staticAnalysis()
                }
            }
        }
        
                stage('Imprimir ENV') {
                    steps {
                        sh 'echo "El worspace es: $WORKSPACE"'
                    }
                }
            }
        }
        stage('Esperar Calidad') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Configurar archivo') {
            steps {
              withCredentials([usernamePassword(credentialsId: 'Credentials_Threepoints', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                sh '''
                  echo "[credentials]" > credentials.ini
                  echo "user=${USER}" >> credentials.ini
                  echo "password=${PASS}" >> credentials.ini
                '''
              }
              archiveArtifacts artifacts: 'credentials.ini', fingerprint: true
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
