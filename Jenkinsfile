pipeline {
    agent {
        label 'Java-17'
    }

    stages {
        stage('Clone the repository') {
            steps {
               git branch: 'build-and-push-to-nexus-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
            }
        }
        
        stage('Build the code') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Push the artifacts into Nexus artifactory') {
            steps {
               nexusArtifactUploader artifacts: [[artifactId: 'microservice-one', classifier: '', file: 'target/microservice-one.war', type: 'war']], credentialsId: 'nexus-cred', groupId: 'com.techworldwithmurali', nexusUrl: 'nexus.techworldwithmurali.in', nexusVersion: 'nexus3', protocol: 'https', repository: 'tech-snapshots', version: '1.0-SNAPSHOT'
               
            }
        }
    }
}
