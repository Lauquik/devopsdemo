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
           
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat 'docker push %IMAGE_NAME%'
                }
            }
        }

        stage('Deploy to K8s') {
                steps {
                    withKubeConfig([credentialsId: 'k8s-kubeconfig']) {
                        bat 'kubectl config view'
                        bat 'kubectl cluster-info'
                        bat 'kubectl apply -f k8s/'
                }
            }
        }

        stage('Verify Deployment') {
                withKubeConfig([credentialsId: 'k8s-kubeconfig']) {
                bat 'kubectl get pods'
            }
        }
    }
}