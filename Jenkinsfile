pipeline {
    agent {
        label 'Java-17'
    }
   

    environment {
        // Define IMAGE_TAG globally using the GIT_COMMIT environment variable
        IMAGE_TAG = "${GIT_COMMIT.substring(0, 6)}"
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
