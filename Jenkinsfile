pipeline {
    agent any // Runs directly on your Linux machine

    tools {
        // This tells Jenkins to use the Maven installed on the machine
        maven 'MAVEN_HOME' 
	git 'GIT_HOME'
   }

    stages {
	// Clean the exiting workspace nefore next build
	stage('Clean Workspace') {
	    steps{
		cleanWs()
		}
	}

	// Pull the code from github repository
        stage('Checkout Code') {
            steps {
                // Pulls code from your GitHub repo
		// git url: "https://github.com/sunilsaini139/DevOps5.git"
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
	
	stage ('copy the war File') {
	    steps{
		withCredentials ([ sshUserPrivateKey (
					credentialsId: 'dockeradmin-ec2-ssh-key',
					usernameVariable: 'SSH_USER',
					keyFileVariable: 'SSH_KEY'
					)
		]) 
		{
		 sh '''
			cp hello-world/webapp/target/*.war /home/dockeradmin/
		   '''
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
