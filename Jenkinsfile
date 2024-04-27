pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_REGISTRY_URL = 'https://index.docker.io/v1/' // Docker Hub registry URL
        DOCKER_IMAGE_NAME = 'bhagyashreep/nodeapp_test' // Docker image name
        DOCKER_IMAGE_TAG = 'latest' // Docker image tag
    }

    stages {
        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                        sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD} ${DOCKER_REGISTRY_URL}"
                    }
                }
            }
        }

        stage('Build and Push Docker Image') {
            agent {
                label 'Docker-Worker' 
            }
            steps {
                script {
                    // Clone GitHub repository
                    git 'https://github.com/bhagyashree3235/nodeapp_test.git'
                    
                    // Path to the Dockerfile relative to the root of the cloned repository
                    def dockerfilePath = './dockerfile'

                    // Build Docker image using the specified Dockerfile
                    sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} -f ${dockerfilePath} ."
                    
                    // Push Docker image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            // Log out from Docker Hub after pushing the image
            sh "docker logout ${DOCKER_REGISTRY_URL}"
        }
    }
}
