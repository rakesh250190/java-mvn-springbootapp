pipeline {

    agent { label 'slave1' }

 environment {
        DOCKER_USER=credentials('DOCKER_USER')
        DOCKER_PWD=credentials('DOCKER_PWD')
    }
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/rakesh250190/java-mvn-springbootapp.git'
            }
        }
        stage('Application_Build') {
            steps {
                echo 'Perform Maven Build'
				sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
				sh "docker build -t 250190/springboot-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag 250190/springboot-app:${BUILD_NUMBER} 250190/springboot-app:latest"
            }
        }

		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKER_USER | docker login -u $DOCKER_PWD --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push 250190/springboot-app:latest"
			}
		}
		stage('Deploy to Kubernetes') {
            steps {
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'PoW-Kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f k8sdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
			}	
            }
		}
    }
}
