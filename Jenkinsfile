pipeline {
    agent any // Runs directly on your Linux machine

    tools {
        // This tells Jenkins to use the Maven installed on the machine
        maven 'MAVEN_HOME' 
	git 'GIT_HOME'
   }
   
   environment {
	IMAGE_NAME = "${JOB_NAME}"
	IMAGE_TAG = "${BUILD_ID}"
        REPO_NAME = "sunilssaini139"
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
	stage(' build images'){
	   steps {
		dir ('hello-world') {
		   script {
			sh """
			   docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
			   docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
			   docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${REPO_NAME}/${IMAGE_NAME}:latest
		          """	
		  	}
	 	}
	    }
	}
	//push the bullt image on dockerhub 
	stage('push') {
	   steps{
		withCredentials([ usernamePassword ( credentialsId: 'dockerhub-credentials',
						    usernameVariable: 'DOCKER_HUB_NAME',
						    passwordVaraible: 'DOCKER_HUB_PASS')]){
		sh """
 			echo "${DOCKER_HUB_PASS}" | docker login -u "${DOCKER_HUB_USER}" --password-sdtin
			docker push ${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
			docker push ${REPO_NAME}/${IMAGE_NAME}:latest
			docker logout
		"""
		}
	   }
	}
	
	stage('delete local images') {
	    steps {
		script{
			sh ' docker rmi -f $(docker images -q ) || true'
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
