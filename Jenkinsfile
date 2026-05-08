pipeline {

    agent any

    environment {
        IMAGE_NAME = "laukik2002/demo-app:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Lauquik/devopsdemo'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Login') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }
           
        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Deploy to Kubernetes') {

            steps {

                bat '''
                wsl kubectl apply -f $(wslpath "%WORKSPACE%")/k8s
                '''
            }
        }

        stage('Verify Deployment') {
            steps {

                bat '''
                wsl kubectl get pods
                '''
            }
        }
    }
}