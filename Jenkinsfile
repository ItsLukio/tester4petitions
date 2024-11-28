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
                // Grant execute permissions to mvnw
                sh 'chmod +x ./tester4petitions/mvnw'
            }
        }

        stage('Build') {
            steps {
                // Run Maven commands using the -f option to specify the pom.xml location
                sh './tester4petitions/mvnw -f ./tester4petitions/pom.xml clean'
                sh './tester4petitions/mvnw -f ./tester4petitions/pom.xml compile'
            }
        }

        stage('Test') {
            steps {
                sh './tester4petitions/mvnw -f ./tester4petitions/pom.xml test'
            }
            post {
                always {
                    junit '**/tester4petitions/target/surefire-reports/*.xml'  // Archive test results
                }
            }
        }

        stage('Package') {
            steps {
                sh './tester4petitions/mvnw -f ./tester4petitions/pom.xml clean package'
            }
        }

        stage('Deploy') {
            steps {
                // Verify WAR file exists
                sh 'ls -l tester4petitions/target/*.war'
                
                // Build Docker image
                sh 'docker build -t myapp tester4petitions/.'
                
                // Remove existing container
                sh 'docker rm -f "myappcontainer" || true'
                
                // Run new container
                sh 'docker run --name "myappcontainer" -p 9090:8080 --detach myapp:latest'
            }
        }
    }

    post {
        success {
            // Archive the WAR file after a successful build
            archiveArtifacts artifacts: 'tester4petitions/target/*.war', allowEmptyArchive: true
        }
    }
}
