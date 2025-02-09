pipeline {
    agent any

    environment {
        registry = "050752624842.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Checkout') {
            steps {
               git 'https://github.com/akannan1087/docker-spring-boot.git'
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean compile"
                sh "mvn clean install"
            }
        }

        stage("Sonarqube Analysis ") {
            agent {label 'sonar'}
            steps {
                withSonarQubeEnv('sonar-server') {
                    dir('src') { 
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.java.binaries=main \
                        -Dsonar.projectName="$repoName" \
                        -Dsonar.projectKey="my_project_key"
                        '''
                    }
                }
            }
        }
        
        stage('Building image') {
             agent {label 'sonar'}
      steps{
        script {
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }
        
        stage ("Push to ECR") {
             agent {label 'sonar'}
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
