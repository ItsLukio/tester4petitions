pipeline {
    agent any

    stages {
        // Stage to get the project from GitHub
        stage('Get Project') {
            steps {
                git branch: 'master', url: 'https://github.com/ItsLukio/tester4petitions.git'
            }
        }

        // Stage to debug and display workspace contents
        stage('Debug Workspace') {
            steps {
                sh 'pwd'  // Display current directory
                sh 'ls -la'  // List files and directories in current workspace
            }
        }

                stage('Set Permissions') {
            steps {
                // Grant execute permissions to mvnw
                sh 'chmod +x ./tester4petitions/mvnw'
            }
        }

        // Stage to clean the project and compile dependencies
        stage('Build') {
            steps {
                sh './tester4petitions/mvnw clean'  // Clean the project
                sh './tester4petitions/mvnw compile'  // Compile the project
            }
        }

        // Stage to run tests
        stage('Test') {
            steps {
                sh './tester4petitions/mvnw test'  // Run tests
            }
            post {
                always {
                    junit '**/tester4petitions/target/surefire-reports/*.xml'  // Archive test results
                }
            }
        }

        // Stage to package the project as a WAR file
        stage('Package') {
            steps {
                sh './tester4petitions/mvnw clean package'  // Package the project
            }
        }

        // Stage to deploy the application using Docker
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

    // Post-build actions
    post {
        success {
            // Archive the WAR file after a successful build
            archiveArtifacts artifacts: 'tester4petitions/target/*.war', allowEmptyArchive: true
        }
    }
}
