pipeline {

    agent any

    environment {
        IMAGE_NAME = "laukik2002/demo-app:latest"
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

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f "%WORKSPACE%\\k8s"'
                bat 'kubectl rollout restart deployment demo-app'
                bat 'kubectl rollout status deployment demo-app'
                bat 'kubectl get pods'
            }
        }

        stage('Verify Deployment') {
            steps {
                withKubeConfig([credentialsId: 'k8s-kubeconfig']) {
                    bat 'kubectl get pods'
                }
            }                                        
        }
    }
}