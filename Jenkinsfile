pipeline {
    agent any

    environment {
        registry = "050752624842.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('Checkout') {
            steps {
               git 'https://github.com/Nithishkumar0064/docker-spring-boot.git'
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
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 050752624842.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 050752624842.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo:latest"
                    
                }
            }
        }
        
        
                
           stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install jan-25-chart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
