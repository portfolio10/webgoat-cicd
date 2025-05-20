pipeline {
    agent any
    environment {
        AWS_REGION = "ap-northeast-2" // 서울 리전
        ECR_REPO = "webgoat-ecr"
        IMAGE_TAG = "latest"
        ACCOUNT_ID = "<669155637873>"
        ECR_URI = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    }
    stages {
        stage('Clone from GitHub') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/portfolio10/webgoat-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t webgoat-image .'
                sh 'docker tag webgoat-image ${ECR_URI}:${IMAGE_TAG}'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-ecr-credentials'
                ]]) {
                    sh 'aws --version'
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_URI'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push ${ECR_URI}:${IMAGE_TAG}'
            }
        }
    }
}
