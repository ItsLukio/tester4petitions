pipeline {
    agent any

    stages {
        stage('Get Project') {
            steps {
                git branch: 'master', url: 'https://github.com/ItsLukio/tester4petitions.git'
            }
        }

        stage('Set Permissions') {
            steps {
                sh 'chmod +x ./tester4petitions/mvnw'
            }
        }

        stage('Build') {
            steps {
                sh './tester4petitions/mvnw -f ./tester4petitions/pom.xml clean package'
            }
        }

        stage('Deploy') {
            steps {
                // Use the correct path for the Dockerfile
                sh 'docker build -t myapp -f Dockerfile ./tester4petitions'
                
                // Remove any existing container
                sh 'docker rm -f "myappcontainer" || true'
                
                // Run the new container
                sh 'docker run --name "myappcontainer" -p 9090:8080 --detach myapp:latest'
            }
        }
    }

    post {
        success {
            // Archive the WAR file for reference
            archiveArtifacts artifacts: 'tester4petitions/target/*.war', allowEmptyArchive: true
        }
    }
}
