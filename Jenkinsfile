pipeline {
    agent any
    
    environment {
        ECR_REPO_URL = "797528244478.dkr.ecr.us-east-1.amazonaws.com/ecr_repo"
        TAG = "${IMAGE_NAME}:${BUILD_NUMBER}"
        IMAGE_NAME = "app-django"
        REGION = "us-east-1"
        K8S_YAML_FILE = "app-django.yml"
       
    }

    stages {
        stage('Build, Tag, and Push Docker image to ECR') {
            steps {
                script {
                    sh '''
                    IMAGE_NAME="${IMAGE_NAME}"
                    
                    ECR_REPO_URL="${ECR_REPO_URL}"
                    
                    REGION="${REGION}"
                    
                    K8S_YAML_FILE="${K8S_YAML_FILE}"
                    
                    TAG="${TAG}"

                    
                    echo "Build Docker image"
                    
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .

                    
                    echo "Tag Docker image"
                    
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${ECR_REPO_URL}:${BUILD_NUMBER}

                    
                    echo "Authenticate Docker with ECR"
                    
                    aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ECR_REPO_URL}

                    
                    echo "Push Docker image to ECR"
                    
                    docker push ${ECR_REPO_URL}:${BUILD_NUMBER}

                    
                    echo "Update Kubernetes YAML file with ECR repository URL"
                    
                    sed -i "s#image:.*#image: ${ECR_REPO_URL}:${BUILD_NUMBER}#" ${K8S_YAML_FILE}
                    
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f ${K8S_YAML_FILE}"
                }
            }
        }
    }
}
