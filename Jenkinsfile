pipeline {
    agent any

    environment {
        ECR_REGISTRY = '050752624842.dkr.ecr.us-east-1.amazonaws.com'
        REPO_NAME = 'my-docker-repo'
        IMAGE_TAG = "${BUILD_NUMBER}"  // Automatically use Jenkins build number
    }
    
    stages {
        stage('Checkout') {
            steps {
               git 'https://github.com/akannan1087/docker-spring-boot.git'
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("Login to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 050752624842.dkr.ecr.us-east-1.amazonaws.com"
            
                    
                }
            }
        }

         stage('Push Docker Image to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}") {
                        sh "docker push ${ECR_REGISTRY}/${REPO_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }
        
        stage ("Helm package") {
            steps {
                    sh "helm package springboot"
                }
            }
                
        stage ("Helm install") {
            steps {
                    sh "helm upgrade myrelease-21 springboot-0.1.0.tgz"
                }
            }
    }
}
