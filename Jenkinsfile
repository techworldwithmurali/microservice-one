pipeline {
    agent any
    tools{
        maven 'Maven-3.9.9'
    }
environment {
        // Define IMAGE_TAG globally using the GIT_COMMIT environment variable
        IMAGE_TAG = "${GIT_COMMIT.substring(0, 6)}"
    }

    stages {
    stage('Clone the repo') {
            steps {
                git branch: 'pushing-docker-image-to-dockerhub-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
        
        stage('Build the code') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh '''
               docker build . --tag microservice-one:$IMAGE_TAG
               docker tag microservice-one:$IMAGE_TAG mmreddy424/microservice-one:$IMAGE_TAG
                
                '''
                
            }
        }
   
   
   stage('Push the docker image to Dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USER_NAME')]) {
                sh '''
               docker login -u $DOCKERHUB_USER_NAME -p $DOCKERHUB_PASSWORD
               docker push mmreddy424/microservice-one:$IMAGE_TAG
                
                '''
                
            }
                }
        }
        
    }
}
