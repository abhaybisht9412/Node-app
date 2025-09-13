pipeline {
    agent any

    environment {
        APP_NAME = "node-jenkins-app"
        IMAGE_TAG = "latest"
        AWS_ACCOUNT_ID = "237090007462"
        AWS_REGION = "us-east-1"
        ECR_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${APP_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/abhaybisht9412/Node-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh '''
                docker build -t $APP_NAME:$IMAGE_TAG .
                aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                docker tag $APP_NAME:$IMAGE_TAG $ECR_REPO:$IMAGE_TAG
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh '''
                docker stop $APP_NAME || true
                docker rm $APP_NAME || true
                docker run -d --name $APP_NAME -p 3000:3000 $ECR_REPO:$IMAGE_TAG
                '''
            }
        }
    }
}
