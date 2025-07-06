pipeline {

    agent { label 'slave1' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginPAT')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/Kaviabi-19/banking-finance.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
				sh 'mvn clean package'
				
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
				sh "docker build -t kavipriyabai/bankapp-eta-app:${BUILD_NUMBER} ."
				sh "docker tag kavipriyabai/bankapp-eta-app:${BUILD_NUMBER} kavipriyabai/bankapp-eta-app:latest"
				sh 'docker image list'
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'				
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
            }
        }
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
				sh "docker push kavipriyabai/bankapp-eta-app:latest"                
            }
        }
        stage('Deploy to Kubernetes Cluster') {
            steps {
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
			}				          
            }
        }
    }
}

