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
    }
}
