pipeline {
    agent any
    tools{
        maven 'Maven-3.9.9'
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
               docker build . --tag microservice-one:latest
               docker tag microservice-one:latest mmreddy424/microservice-one:latest
                
                '''
                
            }
        }
        stage('Push to Dockerhub') {
            steps {
                sh '''
               docker login -u mmreddy424 -p Docker@2580
               docker push  mmreddy424/microservice-one:latest
                
                '''
                
            }
        }



        
    }
}
