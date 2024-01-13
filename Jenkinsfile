pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "hello-world-nextjs"
        DOCKER_IMAGE_TAG = "latest"
        ECR_REPO_NAME = "public.ecr.aws/v3a2v2q2/node-js_project"
        AWS_REGION = "us-east-1"
        AWS_ECR_ACCOUNT_ID = "061485165494"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Build Docker image
                    def dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")

                    // Login to AWS ECR
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id', accessKeyVariable: 'AKIAQ4UGNFO3IKV46UGR', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        dockerImage.inside("--entrypoint=''") {
                            sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ECR_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                        }
                    }

                    // Tag the Docker image for AWS ECR
                    docker.withRegistry("https://${AWS_ECR_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com", 'aws-credentials-id') {
                        dockerImage.tag("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}", "${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}")
                    }

                    // Push the Docker image to AWS ECR
                    docker.withRegistry("https://${AWS_ECR_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com", 'aws-credentials-id') {
                        dockerImage.push("${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}")
                    }
                }
            }
        }

    }
    post {
        always {
            // Cleanup Docker images
            script {
                sh "docker rmi ${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
                //sh "docker rmi ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }
    }
}
