pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        AWS_CREDENTIALS_ID = 'aws-credentials'
        ECR_REGISTRY = 'public.ecr.aws/n5t5t4e4'  // Public ECR registry URI
        ECR_REPOSITORY = 'my-web-app'  // Your ECR repository name
        IMAGE_TAG = 'latest'
        KUBE_CONFIG = credentials('kubeconfig')
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/jinkasaru123/my-web-app.git'  // Replace with your actual GitHub repo URL
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", "${AWS_CREDENTIALS_ID}") {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
