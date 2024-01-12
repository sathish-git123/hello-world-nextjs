#pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        ECR_REPO_NAME = 'public.ecr.aws/v3a2v2q2/node-js_project'
        DOCKER_IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '061485165494'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the source code from the GitHub repository
                    checkout scm
                }
            }
        }

        stage('Build and Push to ECR') {
            steps {
                script {
                    // Authenticate Docker with AWS ECR
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id', accessKeyVariable: 'AKIAQ4UGNFO3IKV46UGR', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                    }

                    // Build and push Docker image to AWS ECR
                    sh "docker build -t ${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG} ."
                    sh "docker tag ${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            // Cleanup Docker images
            script {
                sh "docker rmi ${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
                sh "docker rmi ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }
    }
}
