pipeline {
    agent any

    environment {
        IMAGE_NAME = "ellieene/doctorai-news"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        KUBE_CONFIG = credentials('minikube-kubeconfig')
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/ellieene/DoctorNews.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKER_CREDENTIALS_ID) {
                        docker.build("${IMAGE_NAME}").push("latest")
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
                sh 'kubectl rollout status deployment/news-deployment'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}