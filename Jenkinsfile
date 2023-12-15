pipeline {
    agent any
    environment {
        DOCKER_TAG_NAME = 'mtandur/node-app:latest'
    }

    stages {
        stage('ENV') {
          steps {
            sh 'env | sort'
          }
        }
        stage('Docker Build Image') {
            steps {
                echo 'Build Docker Image...'
                sh '''${DOCKER} build -t ${DOCKER_TAG_NAME} .'''
            }
        }
        stage('Docker Push'){
            steps{
                echo "Push Docker image: ${DOCKER_TAG_NAME} to DOCKER HUB"
                sh'''
                    ${DOCKER} push ${DOCKER_TAG_NAME}
                '''
            }
        }
        stage('Delete local image') {
            steps{
                echo "Delete image: ${DOCKER_TAG_NAME} locally"
                sh '''
                    ${DOCKER} rmi -f ${DOCKER_TAG_NAME}
                '''
            }
        }
        stage('Docker Pull Image'){
            steps{
                echo "Pulls latest Docker Image: ${DOCKER_TAG_NAME}"
                sh '''
                    ${DOCKER} pull ${DOCKER_TAG_NAME}
                '''
            }
        }
        stage('Remove Docker Containers') {
            steps {
                script {
                    // Define the Docker container name or pattern to identify containers to be removed
                    def containerName = 'node-app'

                    // Check if the Docker container exists
                    def isRunning = sh(script: "${DOCKER} inspect -f '{{.State.Running}}' ${containerName}", returnStatus: true)

                    // If the container exists, stop and remove it
                    if (isRunning == 0) {
                        echo "Container: ${containerName} is Running Stopping and removing Docker container"
                        sh "${DOCKER} stop ${containerName}"
                        //sh "${DOCKER} rm ${containerName}"
                    } 
                    else {
                        echo "No running Docker container found with the name: ${containerName}"
                    }
                }
            }
        }
        stage('Docker Run'){
            steps{
                echo "Run Docker Container from docker image: ${DOCKER_TAG_NAME}"
                sh '''
                    ${DOCKER} run --rm -d --name node-app -p 80:80 mtandur/node-app
                '''
                echo "Open URL: http://localhost:80"
            }
        }
    }
}
