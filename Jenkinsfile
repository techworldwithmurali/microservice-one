pipeline {
    agent {
        label 'Java-17'
    }

    stages {
        stage('Clone the Repository ') {
            steps {
               git branch: 'build-and-push-to-jfrog-jenkinsfile', credentialsId: 'github-credentials', url: 'https://github.com/techworldwithmurali/microservice-one.git'
               
               
            }
        }
        
        stage('Build the cvode') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Push the artifacts into Jfrog Artifactory') {
    steps {
        script {
            // Get the current date and time in the format: yyyy-MM-dd_HH-mm
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Define the target path with the timestamp
            def targetPath = "tech-snapshots/${currentDate}/"

            // Upload the artifact to JFrog Artifactory with the timestamped path
            rtUpload(
                serverId: 'Jfrog',
                spec: """
                    {
                        "files": [
                            {
                                "pattern": "*.war",
                                "target": "${targetPath}"
                            }
                        ]
                    }
                """
            )
        }
    }
}
  
    }
}
