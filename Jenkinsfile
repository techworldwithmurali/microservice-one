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
                IMAGE_TAG=$(echo $GIT_COMMIT | cut -c1-6)
              docker build . --tag microservice-one:$IMAGE_TAG
              docker tag microservice-one:$IMAGE_TAG 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$IMAGE_TAG
                
                '''
                
            }
        }
        
        stage('Push Docker Image to AWS ECR') {
steps{
                    sh '''
                   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 533267221649.dkr.ecr.us-east-1.amazonaws.com
                   docker push 533267221649.dkr.ecr.us-east-1.amazonaws.com/microservice-one:$IMAGE_TAG
                    '''
            } 

        }
    }
}
