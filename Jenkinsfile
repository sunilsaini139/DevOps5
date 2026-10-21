pipeline {
    agent any // Runs directly on your Linux machine

    tools {
        // This tells Jenkins to use the Maven installed on the machine
        maven 'MAVEN_HOME' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pulls code from your GitHub repo
                checkout scm
            }
        }

        stage('Build WAR File') {
            steps {
                // Navigate to the hello-world folder
                dir('hello-world') {
                    // Build the project. -DskipTests skips tests for speed.
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
    }

    post {
        success {
            // Save the .war file so you can see it in Jenkins
            archiveArtifacts artifacts: 'hello-world/webapp/target/*.war', fingerprint: true
            echo '✅ WAR file built and archived successfully!'
        }
        failure {
            echo '❌ Build failed. Check the logs above.'
        }
    }
}
