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

        // Stage to clean the project and compile dependencies
        stage('Build') {
            steps {
                sh 'mvn clean:clean'  // Clean the project
                sh 'mvn dependency:copy-dependencies'  // Copy dependencies
                sh 'mvn compile'  // Compile the project
            }
        }

        // Stage to run tests
        stage('Test') {
            steps {
                sh 'mvn test'  // Run tests
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
                sh './mvnw package'  // Package the project using Maven wrapper
            }
        }

        // Stage to deploy the application using Docker
        stage('Deploy') {
            steps {
                // Ensure WAR is built
                sh './mvnw clean package'
                
                // Verify WAR file exists
                sh 'ls -l target/*.war'
                
                // Build Docker image
                sh 'docker build -t myapp .'
                
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
            archiveArtifacts artifacts: '**/tester4petitions/target/*.war', allowEmptyArchive: true
        }
    }
}
