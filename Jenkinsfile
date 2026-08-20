pipeline {
    agent any

    environment {
        AWS_REGION     = 'ap-south-1'
        AWS_ACCOUNT_ID = '755086576240'
        ECR_REPO       = 'httpd-app'
        IMAGE_TAG      = "${BUILD_NUMBER}"

        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        IMAGE_URI    = "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'develop',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/manju230/app1-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${ECR_REPO}:${IMAGE_TAG} .'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region ${AWS_REGION} \
                    | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                    docker tag ${ECR_REPO}:${IMAGE_TAG} ${IMAGE_URI}
                    docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_REGISTRY}/${ECR_REPO}:latest
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker push ${IMAGE_URI}
                    docker push ${ECR_REGISTRY}/${ECR_REPO}:latest
                '''
            }
        }
    }
}
