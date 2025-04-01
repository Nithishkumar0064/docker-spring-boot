pipeline {
    agent any

    environment {
        registry = "public.ecr.aws/l0f2t1u0/docker-images"
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
                    sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0f2t1u0 "
                    sh "docker push public.ecr.aws/l0f2t1u0/docker-images:latest"
                    
                }
            }
        }
        
        
                
           stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install new-chart --namespace helm-deployment --create-namespace --set image.tag=latest"
                }
            }
        }
    }
}
