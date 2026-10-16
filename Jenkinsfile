
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'pradipta211/simple-app'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'echo "Build di Windows"'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    powershell '''
                        $securePass = ConvertTo-SecureString $env:DOCKER_PASS -AsPlainText -Force
                        $credential = New-Object System.Management.Automation.PSCredential($env:DOCKER_USER, $securePass)
                        $credential.GetNetworkCredential().Password |
                            docker login --username $env:DOCKER_USER --password-stdin

                        if ($LASTEXITCODE -ne 0) {
                            throw "Login Docker Hub gagal"
                        }
                    '''

                    bat "docker push %IMAGE_NAME%:%BUILD_NUMBER%"

                    bat "docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest"

                    bat "docker push %IMAGE_NAME%:latest"
                }
            }
        }
    }
}
