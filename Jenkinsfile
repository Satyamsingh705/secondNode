pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS' // Make sure this tool is configured in Jenkins
    }
    
    environment {
        DOCKER_HUB_CREDS = credentials('satyamkumar7050')
        DOCKER_IMAGE = "satyamkumar7050/docker-frontend"
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            }
        }
        
        stage('Docker Push') {
            steps {
                sh "echo ${DOCKER_HUB_CREDS_PSW} | docker login -u ${DOCKER_HUB_CREDS_USR} --password-stdin"
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker push ${DOCKER_IMAGE}:latest"
            }
        }
        
        stage('Deploy') {
            steps {
                sh "docker stop frontend-container || true"
                sh "docker rm frontend-container || true"
                sh "docker run -d -p 3000:3000 --name frontend-container ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker logout"
            }
        }
    }
}
