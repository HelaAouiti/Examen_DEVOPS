pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'helaaouiti'
        FRONTEND_REPO = 'frontend'
        BACKEND_REPO = 'backend'
        SSH_CREDENTIALS = 'ssh-credentials'
        REMOTE_HOST = 'your-recette-server-ip'
        IMAGE_TAG = ''
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'http://github.com/HelaAouiti/Examen_DEVOPS.git', branch: 'main'
            }
        }

        stage('Get Version') {
            steps {
                script {
                    IMAGE_TAG = getVersion()
                    echo "Version: ${IMAGE_TAG}"
                }
            }
        }
		
		stage('Build and Push Backend') {
            steps {
                dir('spring-boot-server') {
					withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                        bat "docker build -t ${DOCKERHUB_USERNAME}/${BACKEND_REPO}:${IMAGE_TAG} ."
                        bat "docker login -u ${DOCKERHUB_USERNAME} -p ${dockerhubpwd}"
                        bat "docker push ${DOCKERHUB_USERNAME}/${BACKEND_REPO}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Build and Push Frontend') {
            steps {
                dir('angular-14-client') {
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                        bat "docker build -t ${DOCKERHUB_USERNAME}/${FRONTEND_REPO}:${IMAGE_TAG} ."
                        bat "docker login -u ${DOCKERHUB_USERNAME} -p ${dockerhubpwd}"
                        bat "docker push ${DOCKERHUB_USERNAME}/${FRONTEND_REPO}:${IMAGE_TAG}"
                    }
                }
            }
        }

	}
    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
def getVersion() {
    // Exécute la commande sans afficher la commande elle-même dans la sortie
    def version = bat(
        script: 'git rev-parse --short HEAD',
        returnStdout: true
    ).trim().split('\n')[-1] // Récupère uniquement la dernière ligne de la sortie
    return version
}