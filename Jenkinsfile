@Library('threepoints-shared-lib@main') _

pipeline {
    agent any

    parameters {
        booleanParam(
            name: 'ABORT_ON_QUALITY_GATE',
            defaultValue: false,
            description: 'Abort pipeline if Quality Gate fails'
        )
    }

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
                            staticAnalysis(false)
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
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: params.ABORT_ON_QUALITY_GATE
                }
            }
        }

        stage('Configurar archivo') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'Credentials_Threepoints',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                        echo "[credentials]" > credentials.ini
                        echo "user=${USER}" >> credentials.ini
                        echo "password=${PASS}" >> credentials.ini
                    '''
                }
                archiveArtifacts artifacts: 'credentials.ini', fingerprint: true
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    def containerName = 'myapp'
                    def imageName = 'myapp:latest'

                    // Stop and remove if running
                    def isRunning = sh(
                        script: "docker ps --filter 'name=${containerName}' --format '{{.Names}}'",
                        returnStdout: true
                    ).trim()

                    if (isRunning) {
                        echo "Stopping existing container '${containerName}'..."
                        sh "docker stop ${containerName}"
                    } else {
                        echo "No running container named '${containerName}' found."
                    }

                    // Start fresh
                    echo "Starting container '${containerName}' from image '${imageName}'..."
                    sh "docker run -d --rm -p 3000:3000 --name ${containerName} ${imageName}"
                }
            }
        }
    }
}
