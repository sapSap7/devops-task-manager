pipeline {
    agent any
        
        triggers {
            githubPush()
        }


    environment {
        AWS_REGION = 'il-central-1'
        ECR_REGISTRY = '064885961914.dkr.ecr.il-central-1.amazonaws.com'
        ECR_REPO = 'devops-task-manager'
        VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Images') {
            steps {
                bat "docker build -t %ECR_REGISTRY%/%ECR_REPO%:backend-%VERSION% ./backend"
                bat "docker build -t %ECR_REGISTRY%/%ECR_REPO%:frontend-%VERSION% ./frontend"
            }
        }

        stage('Push to ECR') {
            steps {
                withCredentials([
                string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat "aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin %ECR_REGISTRY%"
                    bat "docker push %ECR_REGISTRY%/%ECR_REPO%:backend-%VERSION%"
                    bat "docker push %ECR_REGISTRY%/%ECR_REPO%:frontend-%VERSION%"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat "kubectl --kubeconfig C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config set image deployment/backend backend=%ECR_REGISTRY%/%ECR_REPO%:backend-%VERSION%"
        bat "kubectl --kubeconfig C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config set image deployment/frontend frontend=%ECR_REGISTRY%/%ECR_REPO%:frontend-%VERSION%"
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