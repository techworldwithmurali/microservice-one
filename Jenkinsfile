pipeline {
    agent any

    stages {
    stage('Clone the repo') {
            steps {
                git branch: 'pushing-docker-image-to-dockerhub-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
    }
}
