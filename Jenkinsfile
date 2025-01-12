pipeline {
    agent any
    environment {
        BACKEND_VERSION = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        FRONTEND_VERSION = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
    }
    stages {
        stage('Build Backend') {
            steps {
                dir('spring-boot-server') {
                    sh 'docker build -t spring-boot-server:${BACKEND_VERSION} .'
                }
            }
        }
        stage('Push Backend Image') {
            steps {
                sh 'docker tag spring-boot-server:${BACKEND_VERSION} helaaouiti/spring-boot-server:${BACKEND_VERSION}'
                sh 'docker push helaaouiti/spring-boot-server:${BACKEND_VERSION}'
            }
        }
        stage('Build Frontend') {
            steps {
                dir('angular-14-client') {
                    sh 'docker build -t angular-14-client:${FRONTEND_VERSION} .'
                }
            }
        }
        stage('Push Frontend Image') {
            steps {
                sh 'docker tag angular-14-client:${FRONTEND_VERSION} helaaouiti/angular-14-client:${FRONTEND_VERSION}'
                sh 'docker push helaaouiti/angular-14-client:${FRONTEND_VERSION}'
            }
        }
        stage('Deploy') {
            steps {
                sh 'scp docker-compose.yaml user@recette-machine:/path/to/deployment/'
                sh 'ssh user@recette-machine "docker-compose pull && docker-compose up -d"'
            }
        }
    }
}
