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
       stage('Clone the repository') {
            steps {
               git branch: 'pushing-docker-image-to-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh '''
             
              docker build . --tag microservice-one:$IMAGE_TAG
          docker tag microservice-one:$IMAGE_TAG jfrog.techworldwithmurali.in/tech/microservice-one:$IMAGE_TAG
                
                '''
                
            }
        }
        
          stage('Push the docker image to jfrog') {
            steps {
                sh '''
                
             docker login -u devops -p Techworld@2580 jfrog.techworldwithmurali.in
          docker push  jfrog.techworldwithmurali.in/tech/microservice-one:$IMAGE_TAG
                
                '''
                
            }
        }
        
    }
}
