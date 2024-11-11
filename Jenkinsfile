pipeline {
    agent any
    tools {
        maven 'Maven-3.9.9'
    }

    stages {
     stage('Clone the repository') {
            steps {
               git branch: 'pushing-docker-image-to-ecr-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
        
        stage('Build the application') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh '''
              docker build . --tag microservice-one:$GIT_COMMIT
              docker tag microservice-one:$GIT_COMMIT 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$GIT_COMMIT
                
                '''
                
            }
        }
        
        stage('Push Docker Image to AWS ECR') {
steps{
                    sh '''
                   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267221649.dkr.ecr.us-east-1.amazonaws.com
                   docker push 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$GIT_COMMIT
                    '''
            } 

        }
    }
}
