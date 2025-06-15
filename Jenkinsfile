pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '453182569154'
        AWS_REGION = 'ap-southeast-2'
        ECR_REPOSITORY = 'paymentservice'
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

        stage('Create ECR Repository If Not Exists') {
            steps {
                script {
                    sh """
                        aws ecr describe-repositories --repository-names ${ECR_REPOSITORY} --region ${AWS_REGION} || \
                        aws ecr create-repository --repository-name ${ECR_REPOSITORY} --region ${AWS_REGION}
                    """
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh """
                            docker build -t dcgnohp/paymentservice:${IMAGE_TAG} .
                            docker tag dcgnohp/paymentservice:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push dcgnohp/paymentservice:${IMAGE_TAG}"
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
