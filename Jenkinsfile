pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'hheellaa7'
        DOCKERHUB_USERNAME = 'helaaouiti'
        FRONTEND_REPO = 'etude_de_cas2/angular-14-client'
        BACKEND_REPO = 'etude_de_cas2/spring-boot-server'
        SSH_CREDENTIALS = credentials('ssh-credentials')
        REMOTE_HOST = 'your-recette-server-ip'
        IMAGE_TAG = ''
    }	

    stages {

        stage ('Clone Stage') {
            steps {
                git http://github.com/HelaAouiti/Examen_DEVOPS.git'
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
                    script {
                        bat 'docker build -t ${DOCKERHUB_USERNAME}/${BACKEND_REPO}:${IMAGE_TAG} .'
                        bat 'docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_CREDENTIALS}'
                        bat 'docker push ${DOCKERHUB_USERNAME}/${BACKEND_REPO}:${IMAGE_TAG}'
                    }
                }
            }
        }

        stage('Build and Push Frontend') {
            steps {
                dir('angular-14-client') {
                    script {
                        bat 'docker build -t ${DOCKERHUB_USERNAME}/${FRONTEND_REPO}:${IMAGE_TAG} .'
                        bat 'echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_CREDENTIALS}'
                        bat 'docker push ${DOCKERHUB_USERNAME}/${FRONTEND_REPO}:${IMAGE_TAG}'
                    }
                }
            }
        }

        stage('Deploy to Recette') {
            steps {
                sshagent(['ssh-credentials']) {
                    bat """
                        ssh -o StrictHostKeyChecking=no ${SSH_CREDENTIALS_USR}@${REMOTE_HOST} 'mkdir -p ~/deployment'
                        scp docker-compose.yaml ${SSH_CREDENTIALS_USR}@${REMOTE_HOST}:~/deployment/
                        ssh ${SSH_CREDENTIALS_USR}@${REMOTE_HOST} 'cd ~/deployment && docker-compose pull && docker-compose up -d'
                    """
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
    def version = bat(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    return version
}
