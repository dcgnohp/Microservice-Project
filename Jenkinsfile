pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '453182569154'
        AWS_REGION = 'ap-southeast-2'
        ECR_REPOSITORY = 'adservice'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Login to Amazon ECR') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh """
                            docker build -t dcgnohp/adservice:${IMAGE_TAG} .
                            docker tag dcgnohp/adservice:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push dcgnohp/adservice:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Push to Amazon ECR') {
            steps {
                script {
                    sh """
                        docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
