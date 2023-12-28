pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        ECR_REPO = 'public.ecr.aws/v3a2v2q2/node-js_project'
        IMAGE_NAME = 'hello-world-nextjs'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${ECR_REPO}/${IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'ed4a5285-f259-48dd-bbbe-f87f6005fafa', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        docker.withRegistry("https://${061485165494}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com", 'ecr-credentials') {
                            docker.image("${ECR_REPO}/${IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                        }
                    }
                }
            }
        }
    }
}

