pipeline {
    agent {
        label 'Java-17'

    }

    stages {
         stage('Clone the repository'){
        steps{
          git branch: 'static-code-analysis-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
          
        } 
      }
      stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Static code analysis') {
            steps {
        withSonarQubeEnv('sonarqube') {
                    sh '/opt/sonar-scanner/bin/sonar-scanner -Dproject.settings=sonar.properties'
                }
                }
                
            }
    }
}
